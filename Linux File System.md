https://www.youtube.com/watch?v=HbgzrKJvDRw

| Directory           | Description                                                                                                                                                 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/bin`              | really basic binaries like `ls` and `cat`                                                                                                                   |
| `/sbin`             | system binaries that system administrators use                                                                                                              |
| `/boot`             | All the files you need to boot into linux, your bootloaders live here                                                                                       |
| `/cdrom`            | is legacy mounting point                                                                                                                                    |
| `/dev`              | here you will find your hardware (that's why when mounting things you'll often see `dev/sda` or `dev/nvme`)                                                 |
| `/etc`              | where all your system wide configurations are stored.                                                                                                       |
| `/home`             | the home directory, where your stuff is stored!                                                                                                             |
| `/lib`              | libraries are files that applications can use to do various functions                                                                                       |
| `/mnt` and `/media` | This is where you can find your other mounted drives. if you are mounting things manually you use `mnt` if you are doing it automatically you use `/media`. |
| `/opt`              | manually installed software find itself here.                                                                                                               |
| `/proc`             | sudo files about system processes and resources, every process has a directory which is named their process id. If you are a developer this is very handy.  |
| `/root`             | root users home folder, doesn't include the typical files and folders.                                                                                      |
| `/run`              | fairly new. this runs in RAM, use for processes activated early in the boot process.                                                                        |
| `/snap`             | where snap applications are stored.                                                                                                                         |
| `/srv`              | if you run a server you put all the stuff you want the users to access here (like a web server.)                                                            |
| `/sys`              | been around for a long time with the kernel. it is created everytime the system boots up                                                                    |
| `/tmp`              | where files are temporarily stored by application, usually empty.                                                                                           |
| `/usr`              | user application space, applications used by the user are installed here. The `bin` and `sbin` directories are mainly used by the system.                   |
| `/var`              | files and directories that are expected to grow in size like logs and crash reports.                                                                        |


