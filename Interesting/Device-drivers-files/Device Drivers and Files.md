

##### Device Drivers 

*Device driver* is basically executable software. Think of it as a translator between the OS and the piece of Hardware. 

**Linux does have drivers** (regarding your misconception that device files are drivers). Linux distros often come with common drivers already embedded in the kernel (Wi-Fi, sound, basic graphics). Proprietary drivers like NVIDIA (Ubuntu incident Aware) need to be installed separately. These drivers run in the Kernel space.

##### Device Files

*Device Files* allow an application program to interact with a device using it's device driver via input/output system calls. Meaning of the device file is determined by the device number. 

a) **Major Number** --> tells us the driver responsible for the file  
b) **Minor Number** -->  Distinguishes between different physical devices managed by the same driver. for example a hard disk might have different partitions so `/dev/sda1`, `/dev/sda2` will have unique minor numbers. 
 
1) Character Files
	_Character special files_ or _character devices_ provide unbuffered, direct access to the hardware device. They do not necessarily allow programs to read or write single characters at a time; that is up to the device in question.
	
	
	**Who reads `/dev/input/mice`?** Only **One** privileged process (The Display Server)
	**Who gets the data?** Everyone, eventually. But they get a "processed message" delivered by the Display Server, not the raw stream from the file.

2) Block Files
	_Block special files_ or _block devices_ provide buffered access to hardware devices, and provide some abstraction from their specifics. The downside is that because block devices are buffered, the programmer does not know how long it will take before written data is passed from the kernel's buffers to the actual device, or indeed in what order two separate writes will arrive at the physical device. Block device files provide access to devices at the block level, meaning that data is read from and written to the device in fixed-size blocks.


Think of a block device as a hard disk where you read and write one block of data at a time and, the character device is a serial port. You send one byte of data and other side receives that byte and then the next, and so forth and so on.

In each case, when the kernel loads the correct driver (either at boot time, or via programs like [udev](http://en.wikipedia.org/wiki/Udev)) it scans the various buses to see if any devices handled by that driver are actually present on the system. If so, it sets up a device that 'listens' on the appropriate major/minor number.


- Interesting device files (Pseudo-devices)
	Some device files under `/dev` don't correspond to hardware devices. One that exists on every Unix system is `/dev/null`; writing to it has no effect, and reading from it never returns any data. It's often convenient in shell scripts, when you want to ignore the output from a command (`>/dev/null`) or run a command with no input (`</dev/null`). Other common examples are `/dev/zero` (which returns null bytes _ad infinitum_) [`/dev/urandom`](https://serverfault.com/questions/283294/how-to-read-in-n-random-characters-from-dev-urandom) (which returns random bytes _ad infinitum_).