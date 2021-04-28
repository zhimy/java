tar xf local-yum.tar  

cd local-yum/  

chmod +x *  

rpm -ivh libxml2-python-2.9.1-6.el7_2.3.x86_64.rpm

rpm -ivh deltarpm-3.6-3.el7.x86_64.rpm  

rpm -ivh python-deltarpm-3.6-3.el7.x86_64.rpm  

rpm -ivh createrepo-0.9.9-26.el7.noarch.rpm



tar xf gcc-package.tar

cd gcc-package/

createrepo /data/tool/gcc-package

cd /etc/yum.repos.d/

mkdir bak

mv * bak/

tee gcc.repo



[GCC]
name=CentOS7.3 gcc
baseurl=file:///data/gcc-package/
enable=1
gpgcheck=0



yum clean all

yum makecache

yum install gcc

gcc -v