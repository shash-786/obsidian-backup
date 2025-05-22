When you work in Python projects you probably should use a **virtual environment** (or a similar mechanism) to isolate the packages you install for each project.

command = /usr/bin/python3 -m venv /home/shashanksalian/Learning/python/intro/virtual-environment/.venv

```
# activate the venv
source ./venv/bin/activate

# deactivate
deactivate
```
Once the virtual ***environment*** is active any packages installed will be installed in the virtual environment rather than in the global python environment. This allows you to separate set of packages for each project without it affecting the packages installed in the global environment.

### Requirements.txt

In addition to creating and activating a virtual environment, it can be useful to create a Requirements.txt file that lists the packages and their versions that your project depends on. This file can be used to easily install all the required packages in a new environment.
```
(venv) ~ pip freeze                                                              
	is-prime==1.0.1
	numpy==2.2.6
	pandas==2.2.3
	python-dateutil==2.9.0.post0
	pytz==2025.2
	six==1.17.0
	tzdata==2025.2

(venv) ~ pip freeze > requirements.txt
	.
	.
	.

(Some other computer)
(venv) ~ pip install -r requirements.txt
```




