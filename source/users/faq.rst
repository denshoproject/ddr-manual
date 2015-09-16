.. _guide:

==========================
Frequently Asked Questions
==========================


How do I gather and send information about errors to the developers?
--------

First, if the UI generates an error make a PDF of the page. The Django error output has a yellow message area at the top of the page, then a long list aka "stack trace." In the standard view, a lot of the information may be hidden in collapsed sections -- be sure to open each of these before making the PDF.

Next, gather the DDR logfile aka "local.log." This file can be found on the VM's filesystem at: `/var/log/ddr/local.log`

To find and send the log:

1. Log into the VM through the console as `root` or other privileged user.

2. Navigate to the logfile. You should see the file `local.log`

```
root@kinkura:/# cd /var/log/ddr
root@kinkura:/var/log/ddr# ls
celeryd.log
local.log
```

3. Make a copy of the current logfile.

```
root@kinkura:/var/log/ddr# cp local.log local.log.20150916-error
```

4. Copy the error log you just created to the DDR shared directory (`/media/sf_ddrshared`).

```
root@kinkura:/var/log/ddr# cp local.log.20150916-error /media/sf_ddrshared/
```

5. From the Windows desktop, navigate to the shared directory (`C:\ddrshared`). 

6. Send the error logfile along with the UI PDF via email.
