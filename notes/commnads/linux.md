## Reference:

- The 50 Most Popular Linux & Terminal Commands - Full Course for Beginners Link
- The Linux Command Handbook – Learn Linux Commands for Beginners Link

**Note:** Try to use man {command} or tldr {command} before running any command. It will help getting used to other options of that command. 

Extra Info:

- /  (root)
- `~` (home, pronounced as tel di, if I type cd ~ on terminal, it will navigate me to my username folder).
- Everything created by a user (normally) resides in /home/{username} folder
- We can pass -v to `cp, mv, rmdir, rm` command to see the response after running the command. If they really can successfully execute the command. We can turn this on for all the commands in terminal.
- `-r` means recursive. It is helpful when we need to work on folder under folder. It is applied all the sub directories recursively. 
- `-i` means interactive. It helps the user to interact with the command execution. Like rm -i.
We can combine multiple options al-together, like -riv => recursive, interactive, verbose and order of the options is not matter at all.
- `.` means current folder. ../ means one folder back.
- We can combine multiple commands into one, like - `cat hello.txt hi.txt | wc -l >> config.txt`
Piping is very powerful. So, try to use it very often.
- Expansion : `~` means tel di. And it refers to the user's home directory. So, when we type cd ~, our shell expand it to 'cd /users/nowshadhasanapu'. It is called expansion. For printing really ~, we need write -> echo '~'. We can echo other expansions also, like -> echo ~, echo $PATH, echo $USER. echo * - means all files printing in the shell. We can narrow it down, like - echo *.txt -> printing all the .txt file names in the shell. ? means single character expansion, like - echo *.??? - print all the file names with 3 character name extensions, like .txt, .zip files. Not only echo,  We can also use it with rm *.txt to remove all the text files. It's very powerful. Again, we can use echo {a,b,c}.txt to print a.txt, b.txt, c.txt. Or vice versa, like - touch app.{html, js, css} Instead of echo, we can use any of the popular command. And, echo {1..99} gonna print 1 2 3 to 99. So, to create 1000 txt files, we can write the command touch {1…1000}.txt, BOOM!. It will create all in a second. 
- Tebibyte is not like terabyte. 1 TIB = 1.1 TB. 
- `find / -ctime -1 > AllChanged.txt` = means that find in root directory those have changed in last 1 day and write into AllChanged.txt file. 
- `&` - means some task is running in background. And we can also send some process in background by `&`.
- `ps auxf | sort -nr -k 3 | head -10` => 10 cpu extensive process. We can alias this.
- `ncal`/`cal` - gives us the calender. `date` give the date.
- `x` = executable
- `which {program-name, like git, bash}` - give us the /bin link location.
- In mac, options should be passed right after the command, then my other arguments.
- To end any process - we type CTRL+C. CTRL also applicable for mac.
- Type vs Which
- which {software name} - will give the installed folder in our pc.
- I think `rmdir {folder}` only works for non-empty directories. If the folder contains any file, it won't work. Then we need to use `rm -r folder` to delete all the inner files. Yes, we can pass `iv` as well.

Commands:

- whoami - my username
- man {command} - display the manual page of any command (Try writing man man). Interestingly, we can install tldr and get the curated information from man page. Actually, man gives us a lots of information which we don't need very often. Honestly, tldr is very useful and powerful. 
- `clear` - clear the terminal. CTRL+L is a nice shortcut for this.
- `pwd` (print working directory) - echo my current location
- list down the files of the folder. We can also pass -h for human readability for size of the files.
	- `ls` 
	- `ls {directory-path} {options, like - lah}` - shows the contents of the directory path without any further navigation. We can see the contents of folders a long way without changing directory. Directory can be absolute, means - from root (/) directory.
	- `ls -a` (-all: don't ignore files or folders starting with . - . hidden files/folders starts with .)
	- `ls -l` (long format of files)
	- `ls -la` (combined above those)
	ls -la shows the contents like below from left side:
    	- file permission
		- number of links on that file
		- owner of the file
		- group of that file
		- file size in bytes
		- file's last modified datetime
		- filename
- `cd` - (change directory) - dive into a folder. Interestingly, we can't get anything with 'man cd' - (in mac we can have it though), but we have to type 'help cd'. 
  - `cd ~` (/home/{username})
  - `cd /` - root folder of pc
  - `.` - means current folder
  - `../../../` - 3 folders back
  - We can also give absolute path to cd, like `cd /home/` 
- `mkdir` - creates a folder. But let's say we want to create a new folder at the root: `/vlc/data/logs`. folder But vlc, the parent folder is not created yet. So, data folder also not present. So, `mkdir /vlc/data/logs` will give an error if we run the command. So, to create the sub folder with the parent folders, we need to run `mkdir -p /vlc/data/logs`. option p for parents. We also can make the use of '~' to create something from home directory (/users/nowshadapu).
- `touch` - Yes, we all know that it creates a file. But interestingly its main purpose is to change the updated timestamps of file. That means if we write the same command 'hello.txt' for the second time, it will just update the creation timestamp, no changes. If no previous file present, then it will create a file with empty data. For the two interesting characteristics, it is called touch. We can create multiple files with single command 'touch file1.txt file2.txt'.
- `rmdir` - it simply deletes a folder which is non-empty. No, '-p' will not remove non-empty directories. We can delete any empty folder only with this command. It is kind of very less powerful.
- `rm` - It removes the files (Or folder). And with this command we can delete non-empty directories and sub-directories with `-r` (recursive) command. `-i` provides interactive mode, `-v` for verbose mode. This -v works for `cp/mv` and other commands. That means It will ask user which one to keep or delete before deleting every files/folders. By the way, rm/ rmdir will remove things which can not be revert back. It will just go away, so we've to careful about that. And, we can only use rm to delete files, folders or anything without using `rmdir` ever. But still if we want, we can use `rmdir`. 
- `open` - it's a mac specific command. `open .` will open the current directory. open something will open anything in a GUI, which can be opened by double click. In ubuntu, we need to type `xdg-open` to do the same thing. 
- `mv` - it basically renames and moves files and folders. One interesting part is -> `mv {file1} {file2} {folder}`, if the last argument is a folder, then the previous files will be cut-paste to that folder. We can use the same v,i command with this, for extra info. As usual, v = verbose, i = interactive. 
- `cp` - it will create a copy of file (or folder) in target directory. To copy folder we need to pass `-r` to copy all the contents under the folder. We can rename files while copying, if we pass the file name after directory, like - `cp hello.txt dir/HELLO.txt`
- `head` - print the first 10 lines (by default) of a file. `-n`, n=number of lines, is specified for defined number lines. It won't work if head (another software command) is installed in the computer. 
- `tail` - print the last 10 lines of a file. same as tail, `-n` could be supplied. But `-f` (Or, -F)can be supplied also to track the file if it has appended more lines. It is useful when we see the log files live.
- `date` - print the current date + time stamps
- `>, >>` - It actually redirects the standard output to a file. > replaces all the contents with the output of any command, where >> appends at the last. Additionally, it creates the file if no file found with the file name. 
- `cat` - it prints out the full contents of a file. The name comes from concatenate  We can supply multiple file names as argument. It will print all the files' contents sequentially. -n is supplied to show the line numbers. Passing -b to number the only non-empty lines. -s to merge multiple new lines into single line. We can also take the benefit of >/>> as we can use cat with multiple files, then redirect all the contents to a new file. cat file1 file2 >> file3.txt.
- `less` - same as `cat` but more ui friendly, scroll like manual page. It is a program unlike `cat` command. `q` for quiting this mode. When scrolling, we can open the content in vim editor, with `v` command. We can open multiple files with less command, like - `less file1 file2`. To navigate these multiple files, need to type `:n - next file`, `:p-previous file`. `F` to watch mode, same as -> 'tail -F'. To exit this watch mode, press `CTRL+C`.
	- space bar - next page
	- b - previous page
	- G - last line of the file
	- g - first line of the file
	- q - to exit
	- /{something} - to search for something 
- `echo` - it prints in terminal what we try to print. like- printf(""), nothing serious. But it has a great use when we write shell script, or redirect the output (>/>>) in a file. We can also echo our $PATH, $JAVA_HOME etc.
- `wc` - word count. We need to pass the filename for which we want word count, character count, byte count. We can supply different types of files, not just only filename.
	- first - number of lines (`-l`)
	- second - number of characters (`-c`)
	- third - number of bytes
	- fourth - name of the file
- `|` - piping (It's a pipe character). We can use the output of one command to pass to another command. like - cat hello.txt hi.txt | wc -l. It will give the line numbers of two files hello.txt, hi.txt. It's kind of streaming in java. It is more useful with other commands, like -> cat hello.txt | wc >> hello-number.txt: It will append the word count of hello.txt to hello-number.txt file.
- `sort` - sort the lines of a file. Basically it organises as per string. So, for numerical numbers we must use `-n` (n for number). For unique elements, we can use `-u` also. `-h` for human readable sort, like sizewised sort of folder, files. We can also use pipe here, like  `sort -n numbers.txt | wc -l >> numbers-line.txt` : it will sort the numbers of numbers.txt file, get the line number and append in numbers-line.txt file.
- `uniq` - get the unique items of a file, when the items are adjacent. That's why it generally uses with sort command. But it has great great use, like with -c (c = count), we can get the repeated numbers alongside with the content. Then, -d (d=duplicate)for repeated items, -u for uniq items. We can use piping here, like - sort uniq.txt | uniq -c | sort -n. It will sort the all items based on the repetition. We may say that,  sort -u {filename} does the same job as sort {filename} | uniq does. But uniq gives us more control over that.

- `diff` - it's like diff of Git. with `-y`, we can see the the difference side by side. `-u` gives the view like git. To compare the directories, pass `-r` for recursive file finding, and with the content -q. That means for directories comparing, -rq is great. 
- `find` - it finds the files, directories etc matches with pattern. Like `find . (location) -name '*.txt'` - will find everything of current directory that has a match with `.txt`. If we want to find files with '7' character anywhere in the name, we need to type - `find . -name '*7*'`. It's kind of regex. We can also pass `-type` here with d (for directory, i.e. `find . -type d` ), f (for files). We can use `-iname` for case insensitivity. `find . -type d -name 'C*'`- -> Will search for directories Start with C in current location. We can combine multiple filters with -or command , like - 'find . -type d -name 'C*' -or -name 'F*' ' -> Will search for previous criteria and the folders starting with F. And, we can also filter by other criteria, like size -> find . -type f -size +100k. We can also filter by mtime (modified time). Again we can execute command after filtering with -> find . -type f -exec ls -l {} \; - > here, \; - is ending of the command, {} - the placeholder where the output will be replaced, ls -l => is the command to run on each output. 
- `grep` - global regular expression point. find - searches for files, but grep searches inside a file. grep {string-to-search/regex} filename. This command searches for the string in any position of the file by default. To show the line number with the match, -n need to pass. And, `grep -nC 2 green file.txt` will give 2 lines before and after the match of green in file.txt file. But if we want to find the match in every files in a directories and sub-directories, we have to write - grep -r (recursive) "hello" . The searching word is case sensitive by default. To make it case insensitive, need to pass -i (like -ri). grep is very powerful for its regex support. We can use regular expression in there, like - email address.
- `du` - disk usage. I think for mac du = du * (show file info individually). Show the size of files, folders. -k for kilobyte, -m for megabyte show. -h for human readable output. We can use pipe in everywhere. Like - `du -mh | sort -h | tail -5`. give me the 5 largest files. -a will show file info from sub-directories too.
- `df` - disk free. It shows the allocated, used, free etc. We can also pass -h for human readable format. 
- `history` - shows the command history with a number. -n for how many commands to show from history. For quick access, we can take the number of history and execute like that - !{number}. We can search of the history like that - history | grep 'command'.
ps - process status. It's a very powerful command. u (like - ps aux/ ps auxww) for processes belonging to specified username.
ps - normal processes initiated by user
ps ax - all the process (but it gets cut off)
ps axww - same as before, but shows the whole process.
ps axww | grep 'java'. We've been using it for years. How beautiful!

	The columns returned by the command, means that - 
The first information is PID, the process ID. This is key when you want to reference this process in another command, for example to kill it.

Then we have TT that tells us the terminal id used.

Then STAT tells us the state of the process:

I - a process that is idle (sleeping for longer than about 20 seconds)
R - a runnable process
S - a process that is sleeping for less than about 20 seconds
T - a stopped process
U - a process in uninterruptible wait
Z - a dead process (a zombie)

If you have more than one letter, the second represents further information, which can be very technical.

It's common to have + which indicates that the process is in the foreground in its terminal. s means the process is a session leader.

TIME tells us how long the process has been running.

top - processes are running in the system. We can pass sort-order there like - top -o mem => It will sort the processes by memory taken. CTRL+C/q to exit the mode.
kill - kill the process. Normally we shourld try for TERM (15-Terminate) the process, if that does not work, then go for KILL (9-KILL).
kill -l | less -> not gonna kill anyone right now, but shows different types of signals in less format. In mac, this command shows the kill commands by numeric order. KILL - 9 position, TERM - 15 position.
SIGTERM (15)- (TERM for mac) terminate the process, in more gentler way, like save the changes and other pre-terminate steps by the process. And it is default. That means, KILL {PID} will send this signal actually. 
SIGKILL (9) - to force quit the process in a brutal way. KILL -9/SIGKILL/KILL {PID}
killall - almost same as kill, but it does not require any PID, but a process name. It is helpful when multiple PID exists with same process name. killall -9/SIGKILL/KILL {process name}.
Let's run this time consuming command in another terminal: find / -ctime -1 > AllChanged.txt. We can stop any process run in the terminal by CTRL+C. But we can pause the process by CTRL+Z, which will be found by jobs command. The process is just paused, not destroyed totally. We can resume it by fg {n}, n= number of the command. If only one job is present, then fg is enough. If we want to run the process in background, bg %{n} is enough. Again run the jobs command, we will see that the process is running. & - that we can send a process into background, like sleep 50 &. Then we can send that to background, foreground.
gzip - Compression, decompression with Lempel - Ziv coding (L277). We can compress a file by the command gzip {filename}. Keep in mind that, it will replace the original file. So, to keep that, we must pass -k. We can unzip it by gzip -d(decompress) {filename} (we can use gunzip command to decompress it also, both are same basically, but gunzip has other options too which is cool). We can pass -v (verbose) to know how much space is reduced by the compression. We can't compress multiple files into single one by this command. If we pass multiple files as argument, then it will create multiple zip files. But we can create all the files for a folder with -r option. N.B. - There are different levels of compression, from 1 (fastest and worst)-9 (slowest and better). Default is 6.
tar (tape archive, old tape I think) - archive multiple files into single file (It is not compressed, just putting the files in a single bag - that's why it's called archive). It solves the gzip command's problem which is only for single file's compression. tar -cf file1 file2 .. . We must specify the output file name with extension unlike gzip command. c = create, f= archive file name with extension. For decompression we have to pass -xf, x for extract. To just see the files in the archive file, we can pass -tf, t = list. To extract the files in a specific directory, we can pass, tar -xf -C directory-name. By the way, right now, if I want to archive multiple files, and then compress it, I have to go to in two steps, like at first tar, then gzip. For unizipping, we've to the go to the two steps again, at first tar, then gunzip/-d. But to make the two steps in single command, we need to write tar -czf outputfile inputfile1 inputfile2..n. And to unzip+unarchive it, we can run the previous same command tar -xf archivefile.
nano: It's the built in editor in terminal. nano {filename} shows the contents of file in that terminal. I can start immediately typing on there. Then type CTRL+X, then it will ask for different file formats. But hit ENTER to replace in the same file. On the other way, we can type CTRL+O to save the new contents in the file. CTRL+W to search word in the file, if we want to go to the next search appearance, hit CTRL+W again. CTRL+K to cut text, CTRL+U to uncut the text. We can also create and type on new file with nano command, like - nano {new-file-name}. It's also same for VIM.
alias - it's similar to git alias. I think I will like this command very much. By default, alias is just saved into that particular terminal session. After closing that, alias will be removed. So, for permanent save, we need to save the aliases into bashrc (linux-ubuntu), zshrc (mac) etc files. Same command, but need to save into the files. These files can be found at /home/{user} folder. After adding the aliases, we need to reload the files with 'source .bashrc' command, or open a new terminal window to get the alias. Interestingly 'man' command is also an alias of 'run-help'. We can see the alias list with 'alias' command. There are interesting concepts with single and double quote. 
double quote - resolved at definition time, i.e. - alias lsthis="ls $PWD". It will save the output to lsthis for first time invoce. Then how much I move to different folder, it will give the same result (kind of annoying!).
single quote - it will be resolved at that particular moment, i.e. - alias lscurrent='ls $PWD'. It will give different result in different folder.
	We can see the above command's difference with alias command, which will make us 
	understands better.
xargs - [Standard Inputs and arguments are not same]. It's and outstanding command so far. For some commands, it gets the input from standard input, I mean like - ls | sort. This sort command gets input from ls's output. But this is input, not the argument like we pass after touch or rm or any other command. But the commands take that standard input (work for pipe) are not many. Like , touch does not take standard input, that means standard pipe won't work here. rm also takes arguments not standard input. So, for that we need to use xargs. Let's say we have separate file's names in a text file (remove.txt) and we need to remove those files on that name of the directory. So we can write 'cat remove.txt | xargs rm'. Another can be, 'find . -size +100k | xargs ls -lh' as ls -lh also takes arguments not standard inputs. -p for printing the confirmation message, -n1 for printing one iteration at a time, 
ln - It's kind of desktop shortcut like in windows. Concept is similar. It's called link. We can create hardlink, which points the exact same memory of the original file. If we change the original, it affects the hardlink and vice-versa (also applicable for symbolic link). Links are not copy at all. If we delete the original file in case of hardlink, hardlink.txt still will be working, no problem at all. Because the hardlink is pointed to the memory not to that file. But for symbolic link,  it won't work, would say that - no such file or directory. That means for symbolic, it directly points to the file, where for hardlink - it points to the memory. By the way, symlinks are present in /usr/bin folder. So, we can navigate and see the links there. If we type 'ls -la' and see starting 'l' in a file type format, we can understand, that is a link for symbolic link. Commands for creating links :-
hardlink - ln original.txt hardlink.txt
softlink - ls -s original.txt hardlink.txt (s for symbolic)
But why these links are necessary? Let's say we have different versions of Java installed in our computer. But after typing command 'java -v' -> it will point to only one version of java. That's way we can create the symbolic links for other versions too.  
who - logged in informations. It is useful when multiple users logged in into a server. Every separate terminal window is considered as another user. 'who -aH' for more information. Though I can't see it on Ubuntu. It's not same as whoami. tty = tele-typewriter
su - switch user. We can change the user via terminal. Command is 'su user-name'. Each user can create files in their own home directory and see the files in other's. But he can't create files in other's home directory (with root permission, they can do). For that we need login as other's user with that command. Interestingly, terminal's text is changed like nowshad@macbook to raisa@macbook. To land directly on a specific user's home directory, we can type 'su -l/-(dash) user-name'. It's just on the particular terminal session like real login. But in other terminal, user will be the one who logged in the computer. To exit from su, we need to type exit/ctrl+d. 
sudo - super user do (substitute user do). sudo -i => start shell as root. In Ubuntu, root user is locked. We can't log in-logout root user in there. Not all of the linux distributions lock the root user. So, we can log into that directly. But if the root user is locked, then we have to log into any user and run commands as root user with sudo command.Being administrator is not same as root user. Sudo is just another extra layer of security, nothing else. But interestingly every user can't use sudo command. Specific (administrative sudoers) users can run this command.
passwd - change password. We can change current user's password, or other user's password (by running sudo). 
chown - change owner. Owner of a file, or directory can read, write, delete the file or folder. If it's a directory, then owner can create files, but other user can't. The command is: chown {owner} {file} - R can be passed if we want to change the ownership recursively to all the files and sub-directories in a directory. Oh, not end yet. Every files/ directories have group owner also. Group is collection of users, who has a separate level of permissions. That means we can set two level of permissions to read/write/delete files. To see the all existing groups, run groups. To change the group, run => chown {owner}:{group} {file/ folder}.
ncal - gives the calendar in terminal. 'ls -l ncal' - for detailed info.
umask - When we create a file, it is created with default permissions. Those permissions can be controlled by umask. 'umask -S' for human readable mode.
type - if we want to know about our command's type and locations, then we can write - type man, type echo. Normally every commands fall in 4 categories.
an executable
a shell built-in program
a shell function
an alias
which - We use many commands that we install from third-party, like - java, docker and these are added in our $PATH. If we want to know their real location, need to type 'which {command}'. It only works for executables stored in disks not for built-in functions, or aliases.

