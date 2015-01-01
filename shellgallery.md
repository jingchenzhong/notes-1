

###ctags
ctags -R --regex-c="/^SYSCALL_DEFINE[0-9]?\(([a-zA-Z0-9_]*).*/sys_\1/"

###dropbear
=generate private key
dropbearkey -t rsa -f ~/.ssh/id_rsa.db
=yank public key
dropbearkey -y -f ~/.ssh/id_rsa.db
=login in
dbclient -y -i ~/.ssh/id_rsa.db user@ip
=trans file
scp -S dbclient -i ~/.ssh/id_rsa fileName user@ip:/dir

###eval
* create variable name
	__var="name"
	eval "export -- \"$__var=firo\""
	set | grep firo
	__var='name'
	bbb='firo'

###expr
expr "$name" : '\(.*\)\.conf'

###find
* find symbols in object 
	find . -name 'a.out' -exec nm -D {} \; -print
	find . -name '*.o' -print0 | xargs -0 nm -A | egrep ' (i|y)$'
=rm
find . -name ‘your_pattern*’ -exec rm -f {} \;
find . -name ‘your_pattern*’ -delete

###id3tag
id3tag -2 *.mp3
for file in *.mp3;do f=$(echo "$file" |awk -F ' ' '{print $2}' | awk -F '.' '{print $1}'); id3tag -s "$f" "$file";  done
exiftool -json Baccano\!\ 永生之酒\(バッカーノ！\)\ ORIGINAL\ SOUNDTRACK\ SPIRAL\ MELODIES/12.緩みきった官能のダンス.mp3


###indent
indent -npro -kr -i8 -ts8 -sob -l80 -ss -ncs *.c *.h

###pipe
find . -type d | while read d; do cnt=$(ls $d | grep tgz | wc -l); echo "$cnt $d"; done | sort -n >stat 

###qemu
qemu -m 512 -kernel bzImage -append “root=/dev/sda” -boot c -hda busybox.img -k en-us


###readelf
readelf -S module.ko

###rdesktop
rdesktop -r clipboard:CLIPBOARD  -g 1366x768 -u administrator 192.168.10.40

###rpm
rpm -qa --qf '%{INSTALLTIME} %-40{NAME} %{INSTALLTIME:date}\n' | sort -n | cut -d' ' -f2-

rpm -qa --queryformat="%30{NAME} %{SIZE}\n" | sort -k 2 -n | awk '{printf("%30s, %d\n", $1,$2/1024/1024)}'

###sed
sed -r ':1;N;$!b1;s/(.*)}/\1xxoo\n}/' t.sh

###smatch
make CHECK="~/smatch/smatch -p=kernel" C=1 bzImage modules | tee b.log

###ssh
mkfifo /tmp/pipe
ssh root@192.168.199.1 "/tmp/data/usr/sbin/tcpdump tcp   -i br-lan -w -  -n -s 0" > /tmp/pipe
sudo wireshark -i /tmp/pipe

###vim
%s/\s\+$//g

###vmware
sudo vmware-modconfig --console --install-all



##ZZZZ
sudo  ip addr add 192.168.0.11/24 label p4p1:1 dev p4p1

sudo mv -f /usr/share/fonts/google-droid /usr/share/fonts/.google-droid



#kvm
qemu-kvm -m 512 -kernel kernel -initrd initrd    -append 'vga=773 selinux=0'  -drive file=root,format=raw,if=virtio