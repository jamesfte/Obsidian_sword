hydra cheat sheet

-l username
-L username test file

-p password
-P password test file

http -> http-get or http-form-post
ssh -> ip ssh
ftp -> ip ftp

template:
hydra -l user -p pass ip http-get/http-form-post/ssh/ftp
hydra -L user.txt -P pass.txt ip http-get/http-form-post/ssh/ftp

to run faster:
-v verbose mode
-vV even more verbose mode
-t Time normally(4-16)
