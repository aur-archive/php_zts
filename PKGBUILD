# Maintainer: André Ribeiro de Miranda <ardemiranda@gmail.com>
# Maintainer: Boris Momčilović <boris.momcilovic@gmail.com>
pkgname=php_zts
pkgver=5.6.3
pkgrel=1
arch=('x86_64' 'i686')
license=('PHP')
url='http://www.php.net'
makedepends=('apache' 'imap' 'postgresql-libs' 'libldap' 'postfix' 'libvpx'
             'sqlite' 'unixodbc' 'net-snmp' 'libzip' 'enchant' 'file' 'freetds'
             'libmcrypt' 'tidyhtml' 'aspell' 'libltdl' 'libpng' 'libjpeg' 'icu'
             'curl' 'libxslt' 'openssl' 'bzip2' 'db' 'gmp' 'freetype2' 'systemd')
source=("http://www.php.net/distributions/php-${pkgver}.tar.xz"
	"http://www.php.net/distributions/php-${pkgver}.tar.xz.asc"
        'php.ini.patch' 'apache.conf' 'rc.d.php-fpm' 'php-fpm.conf.in.patch'
        'logrotate.d.php-fpm')
md5sums=('7635f344145a4edd7dff6ecec795aaea'
         'SKIP'
         '027ea0583243fe7eab3302024e06624a'
         'dec2cbaad64e3abf4f0ec70e1de4e8e9'
         'b01be5f816988fcee7e78225836e5e27'
         'e5624b9867d1d2b2c650c25ab537a0af'
         '07c4e412909ac65a44ec90e7a2c4bade')
pkgdesc='HTML-embedded scripting language'
depends=('pcre' 'libxml2' 'bzip2' 'curl' 'libxslt' 'tidyhtml' 'sqlite' 'net-snmp'
	'aspell' 'postgresql-libs' 'unixodbc' 'freetds' 'libmcrypt' 'libltdl' 'libldap' 'icu'
	'libpng' 'libjpeg' 'freetype2' 'libvpx' 'enchant')
backup=('etc/php-zts/php.ini' 'etc/httpd/conf/extra/php5-zts_module.conf'
	'etc/php/php-fpm.conf' 'etc/php-zts/pear.conf')

build() {
	phpconfig="--srcdir=../php-${pkgver} \
		--config-cache \
		--prefix=/usr \
		--bindir=/usr/bin/php-zts \
		--includedir=/usr/include/php-zts \
		--libdir=/usr/lib/php-zts \
		--sysconfdir=/etc/php-zts \
		--localstatedir=/var \
		--with-layout=GNU \
		--with-config-file-path=/etc/php-zts \
		--with-config-file-scan-dir=/etc/php-zts/conf.d \
		--disable-rpath \
		--mandir=/usr/share/man \
		--without-pear \
		--enable-maintainer-zts \
		--enable-inline-optimization
		"

	phpextensions="--enable-bcmath=shared \
		--enable-calendar=shared \
		--enable-dba=shared \
		--enable-exif=shared \
		--enable-ftp=shared \
		--enable-gd-native-ttf \
		--enable-intl=shared \
		--enable-mbstring \
		--enable-phar=shared \
		--enable-posix=shared \
		--enable-shmop=shared \
		--enable-soap=shared \
		--enable-sockets=shared \
		--enable-sysvmsg=shared \
		--enable-sysvsem=shared \
		--enable-sysvshm=shared \
		--enable-zip=shared \
		--with-bz2=shared \
		--with-curl=shared \
		--with-db4=/usr \
		--with-enchant=shared,/usr \
		--with-freetype-dir=/usr \
		--with-gd=shared \
		--with-gdbm \
		--with-gettext=shared \
		--with-gmp=shared \
		--with-iconv=shared \
		--with-icu-dir=/usr \
		--with-imap-ssl \
		--with-imap=shared \
		--with-jpeg-dir=/usr \
		--with-vpx-dir=/usr \
		--with-ldap=shared \
		--with-ldap-sasl \
		--with-mcrypt=shared \
		--with-mhash \
		--with-mssql=shared \
		--with-mysql-sock=/var/run/mysqld/mysqld.sock \
		--with-mysql=shared,mysqlnd \
		--with-mysqli=shared,mysqlnd \
		--with-openssl=shared \
		--with-pcre-regex=/usr \
		--with-pdo-mysql=shared,mysqlnd \
		--with-pdo-odbc=shared,unixODBC,/usr \
		--with-pdo-pgsql=shared \
		--with-pdo-sqlite=shared,/usr \
		--with-pgsql=shared \
		--with-png-dir=/usr \
		--with-pspell=shared \
		--with-snmp=shared \
		--with-sqlite3=shared,/usr \
		--with-tidy=shared \
		--with-unixODBC=shared,/usr \
		--with-xmlrpc=shared \
		--with-xsl=shared \
		--with-zlib \
		"

	EXTENSION_DIR=/usr/lib/php-zts/modules
	export EXTENSION_DIR
	PEAR_INSTALLDIR=/usr/share/pear-zts
	export PEAR_INSTALLDIR

	cd ${srcdir}/php-${pkgver}

	# adjust paths
	patch -p0 -i ${srcdir}/php.ini.patch
	patch -p0 -i ${srcdir}/php-fpm.conf.in.patch

	# php
	mkdir ${srcdir}/build-php
	cd ${srcdir}/build-php
	ln -s ../php-${pkgver}/configure
	./configure ${phpconfig} \
		--disable-cgi \
		--with-readline \
		--enable-pcntl \
		${phpextensions}
	make

	# cgi and fcgi
	# reuse the previous run; this will save us a lot of time
	cp -a ${srcdir}/build-php ${srcdir}/build-cgi
	cd ${srcdir}/build-cgi
	./configure ${phpconfig} \
		--disable-cli \
		--enable-cgi \
		${phpextensions}
	make

	# apache
	cp -a ${srcdir}/build-php ${srcdir}/build-apache
	cd ${srcdir}/build-apache
	./configure ${phpconfig} \
		--disable-cli \
		--with-apxs2 \
		${phpextensions}
	make

	# fpm
	cp -a ${srcdir}/build-php ${srcdir}/build-fpm
	cd ${srcdir}/build-fpm
	./configure ${phpconfig} \
		--disable-cli \
		--enable-fpm \
		--with-fpm-user=http \
		--with-fpm-group=http \
		${phpextensions}
	make

	# embed
	cp -a ${srcdir}/build-php ${srcdir}/build-embed
	cd ${srcdir}/build-embed
	./configure ${phpconfig} \
		--disable-cli \
		--enable-embed=shared \
		${phpextensions}
	make
}

package() {
	pkgdesc='An HTML-embedded scripting language'
	depends=('pcre' 'libxml2' 'bzip2' 'curl')
	backup=('etc/php-zts/php.ini')

	cd ${srcdir}/build-php
	make -j1 INSTALL_ROOT=${pkgdir} install
	install -d -m755 ${pkgdir}/usr/share/pear-zts
	# install php.ini
	install -D -m644 ${srcdir}/php-${pkgver}/php.ini-production ${pkgdir}/etc/php-zts/php.ini
	install -d -m755 ${pkgdir}/etc/php-zts/conf.d/

	# remove static modules
	rmdir ${pkgdir}/usr/include/php-zts/php/include
	mv ${pkgdir}/usr/share/man/man1/php.1 ${pkgdir}/usr/share/man/man1/php_zts.1.gz
	mv ${pkgdir}/usr/share/man/man1/php-config.1 ${pkgdir}/usr/share/man/man1/php_zts-config.1.gz
	mv ${pkgdir}/usr/share/man/man1/phpize.1 ${pkgdir}/usr/share/man/man1/phpize_zts.1.gz
	mv ${pkgdir}/usr/share/man/man1/phar.1 ${pkgdir}/usr/share/man/man1/phar_zts.1.gz
	mv ${pkgdir}/usr/share/man/man1/phar.phar.1 ${pkgdir}/usr/share/man/man1/phar_phar_zts.1.gz

	# CGI
	install -D -m755 ${srcdir}/build-cgi/sapi/cgi/php-cgi ${pkgdir}/usr/bin/php-zts-cgi

	# APACHE
	install -D -m755 ${srcdir}/build-apache/libs/libphp5.so ${pkgdir}/usr/lib/httpd/modules/libphp5-zts.so
	install -D -m644 ${srcdir}/apache.conf ${pkgdir}/etc/httpd/conf/extra/php5-zts_module.conf


	# FPM
	install -D -m755 ${srcdir}/build-fpm/sapi/fpm/php-fpm ${pkgdir}/usr/sbin/php-zts-fpm
	install -D -m644 ${srcdir}/build-fpm/sapi/fpm/php-fpm.8 ${pkgdir}/usr/share/man/man8/php-zts-fpm.8
	install -D -m644 ${srcdir}/build-fpm/sapi/fpm/php-fpm.conf ${pkgdir}/etc/php-zts/php-fpm.conf
	install -D -m755 ${srcdir}/rc.d.php-fpm ${pkgdir}/etc/rc.d/php-zts-fpm
	install -D -m644 ${srcdir}/logrotate.d.php-fpm ${pkgdir}/etc/logrotate.d/php-zts-fpm
	install -d -m755 ${pkgdir}/etc/php-zts/fpm.d


	# embed
	install -D -m755 ${srcdir}/build-embed/libs/libphp5.so ${pkgdir}/usr/lib/libphp5-zts.so
	install -D -m644 ${srcdir}/php-${pkgver}/sapi/embed/php_embed.h ${pkgdir}/usr/include/php-zts/sapi/embed/php_embed.h
}

# vim:set ts=2 sw=2 et:
