pyIOSXR
=====

In the spirit of [pyEOS](https://github.com/spotify/pyeos) for Arista EOS 
devices and [pyEZ](https://github.com/Juniper/py-junos-eznc) for JUNOS 
devices, pyIOSXR is a python library to help interact with Cisco devices 
running IOS-XR.

Install
=======

To install, execute:

```
pip install pyIOSXR
```

Documentation
=============

### Connect and lock config
Connect to an IOS-XR device and auto-lock config:
```python
>>> from pyIOSXR import IOSXR
>>> device = IOSXR(hostname='lab001', username='ejasinska', password='passwd', port=22, timeout=120)
>>> device.open()
```

### Connect without auto-lock
Connect to an IOS-XR device withoug locking the config:
```python
>>> from pyIOSXR import IOSXR
>>> device = IOSXR(hostname='lab001', username='ejasinska', password='passwd', port=22, timeout=120, lock=False)
>>> device.open()
```

### Lock and unlock manually
```python
If we connected to the device without locking the config, we might want to lock/unlock it later:
>>> device.lock()
>>> ...
>>> device.unlock()
```

### Load and Compare Config
Load a candidate configuration from a file and show the diff that is going to 
be applied when committing the config:
```python
>>> device.load_candidate_config(filename='unit/test/config.txt')
>>> device.compare_config()
---
+++
@@ -704,0 +705,3 @@
+interface TenGigE0/0/0/21
+ description testing-xml-from-file
+!
```

### Discard Candidate Config
If an already loaded configuration should be discarded without committing it,
call discard_config():
```python
>>> device.discard_config()
>>> device.compare_config()
>>>
```

### Load, Compare and Merge Config
If you want to commit the loaded configuration and merge it with the existing 
configuration, call commit_config():
(comment and label is optional parameters)
```python
>>> device.load_candidate_config(filename='unit/test/other_config.txt')
>>> device.compare_config()
---
+++
@@ -704,0 +705,3 @@
+interface TenGigE0/0/0/21
+ description testing-xml-from-the-other-file
+!
>>> device.commit_config(label='my label', comment='my comment')
```

### Merge Config with Timer based autorollback
If you want to commit the loaded configuration with a timed autorollback that
needs to be confirmed use the confirmed= keyword on the commit, parameters is
seconds to wait before autorollback, values from 30 to 300sec.
when using confirmed= you need to do another commit_config() without parameters
within the time spesified to acknowledge the commit or else it rolls back your changes.
(comment and label are optional parameters)
```python
>>> device.load_candidate_config(filename='unit/test/other_config.txt')
>>> device.commit_config(label='my label', comment='my comment', confirmed=30)
.... Code to do checks etc ....
>>> device.commit_config()
```

### Commit Replace Config
If you would rather commit the config by replacing the existing configuration,
call commit_replace_config():
(comment and label is optional parameters)
```python
>>> device.load_candidate_config(filename='unit/test/full_config.txt')
>>> device.compare_replace_config()
>>> device.commit_replace_config(label='my label', comment='my comment')
```

### Rollback Config
After a previous commit, rollback() will return to the configuration prior
to the commit:
```python
>>> device.rollback()
```

### Running Show Commands
Any show command can be executed in the following fashion, with the command 
embedded into the call:
```python
>>> device.show_bgp_summary()
...
>>> device.show_interfaces()
...
```

### Running XML Commands
An arbitrary XML command can be executed with the command:
```python
>>> device.make_rpc_call("<Get><Operational><LLDP><NodeTable></NodeTable></LLDP></Operational></Get>")
...
```

### Close Connection
Call close() to close the connection to the device:
```python
>>> device.close()
```

### Debugging Connection
Log the communication between pyIOSXR and the router to any file-like like stdout, or an actual file:
```python
>>> from pyIOSXR import IOSXR
>>> import sys
>>> device=IOSXR(hostname="router", username="cisco", password="cisco", port=22, timeout=120, logfile=sys.stdout)

OR

>>> from pyIOSXR import IOSXR
>>> import sys
>>> file = open("logfile.log")
>>> device=IOSXR(hostname="router", username="cisco", password="cisco", port=22, timeout=120, logfile=file)
```

Thanks
======
A special thanks to David Barroso! This library is entirely based on David's
[pyEOS](https://github.com/spotify/pyeos), which is a wrapper around Arista's
eAPI for EOS.

Also, many thanks go out to Brady Walsh, without whom staying sane while 
digging through Cisco docs and XML would have been impossible.

License
======

Copyright 2015-2016 Netflix, Inc.

Licensed under the Apache License, Version 2.0: http://www.apache.org/licenses/LICENSE-2.0
