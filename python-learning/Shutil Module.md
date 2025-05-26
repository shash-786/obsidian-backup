## shutil for high-level file operations[](https://compenv.phys.ethz.ch/python/ecosystem_3/12_os_and_shutil/#shutil-for-high-level-file-operations "Link to this heading")

The [shutil](https://docs.python.org/3/library/shutil.html) module of the Python Standard Library offers helper functions for common file operations, like copying or deleting.

Below some usage examples, where `source`, `dest` and `path` are understood to be strings or path-like objects referring to files or directories.

```python
import shutil

shutil.move(source, dest)       # mv source destination
shutil.copy(source, dest)       # cp source destination
shutil.copy2(source, dest)      # cp -p source destination
shutil.copytree(source, dest)   # cp -rp source destination
shutil.rmtree(path)             # rm -r path
shutil.which("python3")         # which python3
shutil.chown(path, user="jdoe", group="jdoe") # chown jdoe:jdoe path
shutil.diskusage(path)          # return total, used and free disk space
```
