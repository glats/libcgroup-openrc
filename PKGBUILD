pkgname=libcgroup-openrc
_pkgname=libcgroup
pkgver=0.41
pkgrel=2
pkgdesc='Tools and libraries to configure and manage kernel control groups with OpenRC support'
arch=('i686' 'x86_64')
url='http://libcg.sourceforge.net'
license=(LGPL)
backup=('etc/cgroup/cgconfig.conf' 'etc/cgroup/cgrules.conf' 'etc/cgroup/cgsnapshot_blacklist.conf')
options=('!emptydirs' '!libtool')
install=libcgroup.install
source=("http://downloads.sourceforge.net/libcg/${_pkgname}-${pkgver/rc/.rc}.tar.bz2"
	'cgconfig.initd'
	'cgred.initd'
	'cgconfig.confd'
	'cgred.confd')
sha256sums=('e4e38bdc7ef70645ce33740ddcca051248d56b53283c0dc6d404e17706f6fb51'
            'f96872615461d91ad5c10e50dbf68ffee130c3bd1bcfdac9b9987d4673c56ce5'
            '540ebbf0fbcc9e37a7d98629d4d4f6cd012350abbabfb8eca79b2dcddab510c7'
            '500b3f3a35eaa94b42ba6f9895498e4bfad44bbf36655782a2de3f78e9e1a23e'
            '52b8d2f417e21c0cd80289286483b554d6ab6bd33d2bb3f279736d869aee6d49')

build() {
	cd "${srcdir}/${_pkgname}-${pkgver/rc/.rc}"

	./configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--localstatedir=/var \
		--sbindir=/usr/bin \
		--enable-opaque-hierarchy=name=openrc

	make
}

package() {
	cd "${srcdir}/${_pkgname}-${pkgver/rc/.rc}"

	make DESTDIR="${pkgdir}" pkgconfigdir="/usr/lib/pkgconfig" install

    install -D -m0755 "${srcdir}/cgconfig.initd" "${pkgdir}/etc/init.d/cgconfig"
	install -D -m0755 "${srcdir}/cgred.initd" "${pkgdir}/etc/init.d/cgred"
	install -D -m644 "${srcdir}/cgconfig.confd" "${pkgdir}/etc/conf.d/cgconfig"
	install -D -m644 "${srcdir}/cgred.confd" "${pkgdir}/etc/conf.d/cgred"
	mkdir -p "${pkgdir}/etc/cgroup"
	install -D -m0644 "samples/cgconfig.conf" "${pkgdir}/etc/cgroup/cgconfig.conf"
	install -D -m0644 "samples/cgrules.conf" "${pkgdir}/etc/cgroup/cgrules.conf"

	# Example configuration files
	mkdir -p "${pkgdir}/usr/share/libcgroup"
	install -D -m0644 "samples/cgrules.conf" "${pkgdir}/usr/share/libcgroup/cgrules.conf"
	install -D -m0644 "samples/cgsnapshot_blacklist.conf" "${pkgdir}/usr/share/libcgroup/cgsnapshot_blacklist.conf"

	rm -f ${pkgdir}/usr/lib/security/pam_cgroup.{la,so,so.0}
	mv ${pkgdir}/usr/lib/security/pam_cgroup.so.0.0.0 ${pkgdir}/usr/lib/security/pam_cgroup.so

	rm -rf ${pkgdir}/etc/rc.d

	# Make cgexec setgid cgred
	chown root:160 ${pkgdir}/usr/bin/cgexec
	chmod 2755 ${pkgdir}/usr/bin/cgexec
}