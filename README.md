# Charlotte SAC Coding Meeting nfl_data_py Intro
Unfortunately, installing this package sucks. It works as intended on Python versions 3.10-12, but to avoid having to install older Python versions, we are going to try to get around this by using this line to install nfl_data_py (If this doesn't work, we are gonna use ChatGPT to figure it out).
```Python
!pip install numpy>=2.0 pandas fastparquet appdirs && pip install nfl_data_py --no-deps
```
You should already have this, but run this line just to make sure.
```Python
!pip install Pillow
```
