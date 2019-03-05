# PostgreSQL

## Arch Linux
### Сборка из исходников
Собираем PostgreSQL поддержкой plpython обоих версий (без проверок, тестов и документации). Опции для подавления вывода полезны, если собираем Docker-образ.

```bash
pacman -Sy >/dev/null
pacman -S base-devel man tar wget git krb5 libxml2 python python2 perl tcl>=8.6.0 openssl>=1.0.0 pam zlib icu libldap --noconfirm --needed >/dev/null
cd /tmp
curl https://ftp.postgresql.org/pub/source/v11.2/postgresql-11.2.tar.bz2 --output postgresql-11.2.tar.bz2 --silent
tar xjf postgresql-11.2.tar.bz2
cd postgresql-11.2
./configure --prefix=/usr --mandir=/usr/share/man --datadir=/usr/share/postgresql --sysconfdir=/etc --with-gssapi --with-libxml --with-openssl --with-perl --with-python --with-tcl --with-pam --with-system-tzdata=/usr/share/zoneinfo --with-uuid=e2fs --with-icu --with-systemd --with-ldap --enable-nls --enable-thread-safety --disable-rpath PYTHON=/usr/bin/python
make world
make install-world
```

### Сборка Ssdeep
Собираем стороннее дополнение на примере Ssdeep

```bash
cd /tmp
wget https://github.com/ssdeep-project/ssdeep/releases/download/release-2.14.1/ssdeep-2.14.1.tar.gz
tar xzf ssdeep-2.14.1.tar.gz
./configure
make
make install

cd /tmp/postgresql-11.2/contrib
cd /tmp/postgresql-11.2/contrib/ssdeep_psql
make
make install
```

### Первичная настройка БД
```bash
mkdir -p /var/lib/postgres/data
useradd -d /var/lib/postgres postgres
chown postgres:postgres /var/lib/postgres
chown postgres:postgres /var/lib/postgres/data
chmod 700 /var/lib/postgres/data
echo 'ru_RU.UTF-8 UTF-8' >> /etc/locale.gen
locale-gen
su - postgres -c "initdb --locale ru_RU.UTF-8 -E UTF8 -D '/var/lib/postgres/data'"
systemctl enable postgresql
systemctl start postgresql
```

## Alpine Linux
### Сборка из исходников
```sh
apk update -q
apk add -q git krb5 libxml2 python3 perl tcl openssl linux-pam zlib icu libldap
cd /
```

