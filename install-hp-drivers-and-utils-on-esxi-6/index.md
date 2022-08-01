---
title: "Install HP Drivers and Utils on ESXi 6"
date: "2017-08-26"
categories: 
  - "vmware"
tags: 
  - "vmware"
---

If you install ESXi on a HP server using the VMware ISO instead of the custom HP ISO you'll need to manually install the HP Utility and Driver VIB's.

Save and execute this Python script in the ESXi shell to automatically download and install these VIB's.

**MAKE SURE YOU'RE IN MAINTENANCE MODE BEFORE EXECUTING**

#!/bin/python
from urllib2 import urlopen
from subprocess import call
from getpass import getpass
import re
import os

print("")
print("!!! Please make sure the server is in maintenance mode !!!")
print("")
trash = getpass("Press ENTER to continue...")
print("")

destdir = '/opt/hp-vibs/'
url = 'http://vibsdepot.hp.com/hpq/latest/esxi-600-bundles/'
pattern = '<a href=".\*">(hp\\-.\*\\.zip)</a>'

# Create destdir if it doesn't already exist
if not os.path.exists(destdir):
  os.makedirs(destdir)

# Read the html of the index url
response = urlopen(url).read()

# Loop through all zip files
for filename in re.findall(pattern, response):
  print('Downloading '+filename)
  if os.path.exists(destdir+filename):
	  os.remove(destdir+filename)  # If the file already exists then delete it
  remotefile = urlopen(url+filename)  # Open the remote file
  localfile = open(destdir+filename, 'wb')  # Open the file for buffered writing
  localfile.write(remotefile.read())  # Read the contents of the remote file and write them to the local file
  localfile.close()  # Flush and close the local file
  remotefile.close()  # Close the remote file
  call(\["/bin/esxcli", "software", "vib", "install", "-d", destdir+filename\])  # Install the file with esxcli

print("")
print("Done, please REBOOT the server to complete the process.")
print("")
