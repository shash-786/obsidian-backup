**Builder** is a creational design pattern that lets you construct complex objects step by step. The pattern allows you to produce different types and representations of an object using the same construction code. is a creational design pattern that lets you construct complex objects step by step. 

The pattern allows you to produce different types and representations of an object using the same construction code.

```cpp
// main.cc
int main (int argc, const char* argv) {
	SECURITY_ATTRIBUTES sa{} ;
	File::Builder b("sample.txt", FILE_GENERIC_READ) ;
	File f = b.FlagsAttributes(FILE_ATTRIBUTE_ENCRYPTED)
			  .CreationDisposition(OPEN_EXISTING)
			  .ShareMode(FILE_SHARE_READ)
			  .SecurityAttributes(&sa)
			  .Build() ;
	char buffer[1024] {} ;
	f.read (buffer, 1024) ;
	std::cout << buffer ;
}
```

### Fluent Modern Builder Implementation 

```cpp
#include <windows.h>

class File {
	const char *m_pFileName{};
	DWORD m_DesiredAccess{} ;
	DWORD m_ShareMode{} ;
	LPSECURITY_ATTRIBUTES m_pSA{} ;
	DWORD m_CreationDisposition{} ;
	DWORD m_FlagsAttributes{}
	HANDLE m_hTemplateFile{};
	
	HANDLE m_hFile{} ;
	
	public:
	// Normal Constructor
	File (
		const char* filename,
		DWORD desiredAccess,
		DWORD sharedMode,
		LPSECURITY_ATTRIBUTES psa,
		DWORD creationDisposition ,
		DWORD flagsAttributes ,
		HANDLE templateFile ,
	)
	
	// Simple File Constructor
	File (
		const char* filename,
		DWORD desiredAccess,
	)
	
	// Custome attribures File
	File (
		const char* filename,
		DWORD desiredAccess,
		DWORD flagsAttributes ,
	)
	
	// MEMBER FUNCTIONS
	DWORD Read(char* pBuffer, DWORD size) ;
	DWORD Write(const char* pBuffer, DWORD size) ;
	void Close() ;
	~File () ;
	BOOL IsOpen() const ;
	
	class Builder {
		const char *m_pFileName{};
		DWORD m_DesiredAccess{} ;
		DWORD m_ShareMode{} ;
		LPSECURITY_ATTRIBUTES m_pSA{} ;
		DWORD m_CreationDisposition{CREATE_ALWAYS} ;
		DWORD m_FlagsAttributes{FILE_ATTRIBUTE_NORMAL}
		HANDLE m_hTemplateFile{};
		
		public:
		Builder (const char* fileName, DWORD desiredAccess) : m_pFileName{fileName}, m_DesiredAccess{desiredAccess} {}
		
		Builder& SharedMode (DWORD sharedMode) {
			m_ShareMode = sharedMode ;
			return *this ;
		}
		
		Builder& SecurityAttributes (LPSECURITY_ATTRIBUTES lpsa) {
			m_pSA = lpsa ;
			return *this ;
		}
		
		Builder& CreationDisposition (DWORD creationDispositon) {
			m_CreationDisposition = creationDispositon ;
			return *this ;
		}
		
		Builder& FlagsAttributes (DWORD flagsAttributes) {
			m_FlagsAttributes = flagsAttributes ;
			return *this ;
		}
		
		Builder& TemplateFile (HANDLE templateFile) {
			m_hTemplateFile = templateFile ;
			return *this ;
		}
		
		File Build () {
			return File (
				m_pFileName ,
				m_DesiredAccess ,
				m_ShareMode ,
				m_pSA ,
				m_CreationDisposition ,
				m_FlagsAttributes ,
				m_hTemplateFile 
			)
		}
	};	 
};
```