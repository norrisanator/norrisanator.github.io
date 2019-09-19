---
layout: post
title: Over the wire Bandit level 0 to 12
---

# Over the wire level Bandit level 0 to 12

http://overthewire.org/wargames/bandit/

**Level 0**  
The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.
```
ssh bandit0@bandit.labs.overthewire.org -p 2220

bandit0
ls cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```
**Level 1**  
The password for the next level is stored in a file called - located in the home directory
```
ssh bandit1@bandit.labs.overthewire.org -p 2220
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
ls
cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```
**Level 2**  
The password for the next level is stored in a file called spaces in this filename located in the home directory
```
ssh bandit2@bandit.labs.overthewire.org -p 2220
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

cat "spaces in this filename"
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```
**Level 3**  
The password for the next level is stored in a hidden file in the inhere directory.
```
ssh bandit3@bandit.labs.overthewire.org -p 2220
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

ls
cd inhere
ls 
ls -a
cat .hidden 
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```
**Level 4**  
The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
```
ssh bandit4@bandit.labs.overthewire.org -p 2220
pIwrPrtPN36QITSp3EQaw936yaFoFgAB

ls
cd inhere
ls
cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```
**Level 5**  
The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:  

human-readable  
1033 bytes in size  
not executable  
```
ssh bandit5@bandit.labs.overthewire.org -p 2220
koReBOKuIDDepwhWk7jZC0RTdopnAYKh

cd inhere
find . -type f -size 1033c ! -executable
https://unix.stackexchange.com/questions/43148/unix-commands-find
./maybehere07/.file2

cat ./maybehere07/.file2

DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```
**Level 6**  
The password for the next level is stored somewhere on the server and has all of the following properties:  

owned by user bandit7  
owned by group bandit6  
33 bytes in size  
```
ssh bandit6@bandit.labs.overthewire.org -p 2220
DXjZPULLxYr17uwoI01bNLQbtFemEgo7

find / -user bandit7 -group bandit6 -size 33c

cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```
**Level 7**  
The password for the next level is stored in the file data.txt next to the word millionth  
```
ssh bandit7@bandit.labs.overthewire.org -p 2220
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

grep "millionth" data.txt
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
**Level 8**  
The password for the next level is stored in the file data.txt and is the only line of text that occurs only once  
```
ssh bandit8@bandit.labs.overthewire.org -p 2220
cvX2JJa4CFALtqS87jk27qwqGhBM9plV

sort data.txt | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```
**Level 9**  
The password for the next level is stored in the file data.txt in one of the few human-readable strings, beginning with several ‘=’ characters. 
```
ssh bandit9@bandit.labs.overthewire.org -p 2220
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

strings data.txt | grep =
2========== the
========== password
>t=	yP
rV~dHm=
========== isa
=FQ?P\U
=	F[
pb=x
J;m=
=)$=
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```
**Level 10**  
The password for the next level is stored in the file data.txt, which contains base64 encoded data
```
ssh bandit10@bandit.labs.overthewire.org -p 2220
truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

cat data.txt | base64 --decode
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```
**Level 11**  
The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions
```
ssh bandit11@bandit.labs.overthewire.org -p 2220
IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```