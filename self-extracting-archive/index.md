---
title: "Self-Extracting Archive"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

I use this script to create a self-extracting script from a directory. Executing the resultant script will extract its contents, execute autorun.sh, then clean up after itself.

**sfxbuild.sh**

#!/bin/bash

# sfxbuild.sh <directory>

# This script generates a self extracting (sfx) installer from a sub-directory.
# The resulting file is a bash script that will extract itself, run a file
# called autorun.sh, then delete the extracted directory.

if \[ -z ${1+x} \]; then
  cat << EOF

Usage: $0 <directory>

<directory> = Directory name to turn into a sfx script

<directory> should contain autorun.sh which will be run immediatly after
  the compressed data is extracted

EOF
  exit 1
fi

# if the sfx file already exists then delete it
if \[\[ -f ./$1.sfx.sh \]\]; then
  rm -fr ./$1.sfx.sh
fi

# Check for autorun.sh
if \[\[ -f ./$1/autorun.sh \]\]; then
  # Make sure it's executable
  chmod +x ./$1/autorun.sh
else
  # It doesn't exist so throw an error
  echo ./$1/autorun.sh does NOT exist!
  exit 1
fi

# create tar.gz file inside of source directory
cd $1
tar zcf $1.tar.gz \*

# move it to the parent directory
mv $1.tar.gz ..
cd ..

# generate header for sfx file
cat << 'EOF' > ./$1.sfx.sh
#!/bin/bash
  # Create a new directory for the sfx content
  mkdir $(basename $0 .sh)
  
  # Extract the binary data from this script to the new directory
  sed '0,/^\_\_BINARY\_\_$/d' $0 | tar zx -C ./$(basename $0 .sh)
  
  # Run the autorun.sh script
  cd $(basename $0 .sh)
  ./autorun.sh
  
  # Remove the extracted data and directory
  cd ..
  \\rm -fr ./$(basename $0 .sh)

  # Exit the script before running into the binary data
  exit 0
    
  # Everything below the next line will be interpreted as a GZipped TAR file
\_\_BINARY\_\_
EOF

# append tar.gz to the sfx script and remove the tar.gz file
cat ./$1.tar.gz >> ./$1.sfx.sh
rm ./$1.tar.gz

# make the sfx file executable
chmod +x ./$1.sfx.sh

# let the user know
echo Generated ./$1.sfx.sh
