# reproduciblemindspore
These tools are intended to make it easier to verify that the binaries resulting from mindSpore builds are reproducible.

For this, we rebuild twice locally from source with variations in

* datetime

within opensource libfaketime in openGauss build system,
unpack the differenc packages and compare the results using the build-compare script (diffoscope) that abstracts away some unavoidable (or unimportant) differences.

Steps:  
1. Install libfaketime：  
get libfaketime source https://github.com/opensourceways/reproducible-builds-libfaketime  
```
make
make install
```
2. To mock datetime, set the libfaketime environment  
```
echo 'export LD_PRELOAD=/usr/local/lib/faketime/libfaketimeMT.so.1' >> /etc/profile
echo 'export FAKETIME="2022-06-01 10:11:20"' >> /etc/profile
```
3. Solve the binary differences caused by time & random numbers in Python during the compilation of source packages  
```
echo 'export SOURCE_DATE_EPOCH=1' >> /etc/profile  
echo 'export PYTHONHASHSEED=0' >> /etc/profile  
```
4. We added a blacklist and whitelist mechanism to libfaketime, In blacklist mode, libfaketime only hooks commands in the blacklist; In white list mode, libfaketime will skip the commands in the white list and hook all other commands.
   Users can use the blacklist or whitelist function by downloading the corresponding branch. For example:
```
# blacklist mode
export BLACKLIST=ls,make

# whitelist mode
export WHITELIST=ls,make
```

5. Rebuild packages twice locally from software source in openGauss build system  

6. Unpack your two packages using the unpacker.py tool  
```
python unpacker.py ${first package path} ${second package path}
```
7. Display differences using diffoscope : 
```
yum install diffoscope  
diffoscope ${first file path} ${second file path} --html diff.html
```

For maven projects, you can use reproducible-build-maven-plugin make that reproducible  
https://maven.apache.org/guides/mini/guide-reproducible-builds.html  
https://github.com/Zlika/reproducible-build-maven-plugin
