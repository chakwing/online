# This file is part of the LibreOffice project.
#
# This Source Code Form is subject to the terms of the Mozilla Public
# License, v. 2.0. If a copy of the MPL was not distributed with this
# file, You can obtain one at http://mozilla.org/MPL/2.0/.

FROM ubuntu:16.04

# get the latest fixes
RUN apt-get update && apt-get upgrade -y

# install LibreOffice run-time dependencies
# install apt-transport-https in order to set up repo for Poco
# install adduser, findutils and cpio that we need later
RUN apt-get -y install apt-transport-https locales-all libpng12-0 libxinerama1 libgl1-mesa-glx libfontconfig1 libfreetype6 libxrender1 libxcb-shm0 libxcb-render0 adduser cpio findutils

# set up 3rd party repo of Poco, dependency of loolwsd
RUN echo "deb https://collaboraoffice.com/repos/Poco/ /" >> /etc/apt/sources.list.d/poco.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0C54D189F4BA284D
RUN apt-get update
RUN apt-get -y install libpoco*60

# copy freshly built LibreOffice master and LibreOffice Online master with latest translations
COPY /instdir /

# copy the shell script which can start LibreOffice Online (loolwsd)
COPY /scripts/run-lool.sh /

# set up LibreOffice Online (normally done by postinstall script of package)
RUN setcap cap_fowner,cap_mknod,cap_sys_chroot=ep /usr/bin/loolforkit
RUN setcap cap_sys_admin=ep /usr/bin/loolmount
RUN adduser --quiet --system --group --home /opt/lool lool
RUN mkdir -p /var/cache/loolwsd && chown lool: /var/cache/loolwsd
RUN rm -rf /var/cache/loolwsd/*
RUN rm -rf /opt/lool
RUN mkdir -p /opt/lool/child-roots
RUN chown lool: /opt/lool
RUN chown lool: /opt/lool/child-roots
RUN su lool --shell=/bin/sh -c "loolwsd-systemplate-setup /opt/lool/systemplate /opt/libreoffice >/dev/null 2>&1"
RUN touch /var/log/loolwsd.log
RUN chown lool /var/log/loolwsd.log
CMD bash /run-lool.sh
