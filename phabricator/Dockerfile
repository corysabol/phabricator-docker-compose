from ubuntu:latest

WORKDIR /opt
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update
RUN apt install -y git lsb-release apt-utils

RUN adduser --disabled-password --gecos '' www-user

COPY ./conf/setup.sh ./setup.sh
RUN chmod +x ./setup.sh
RUN ./setup.sh

COPY ./conf/phabricator.conf /etc/apache2/sites-available/phabricator.conf
RUN ln -s /etc/apache2/sites-available/phabricator.conf /etc/apache2/sites-enabled/
RUN rm /etc/apache2/sites-available/000-default.conf
RUN rm /etc/apache2/sites-enabled/000-default.conf

COPY ./conf/local/local.json /opt/phabricator/conf/local/local.json
COPY ./startup.sh ./startup.sh

RUN useradd daemon-user
#RUN chown -R daemon-user:daemon-user /data/repo
# need to modify the sudoers file to allow passwordless sudo on all git stuffs
# need to symlink git-http-backend into /usr/bin/
RUN ln -s /usr/lib/git-core/git-http-backend /usr/bin/
RUN apt install sudo
RUN echo "Cmnd_Alias GIT_CMDS = /usr/bin/git*" >> /etc/sudoers.d/www-user-git
RUN echo "www-user ALL=(daemon-user) SETENV: NOPASSWD: GIT_CMDS" >> /etc/sudoers.d/www-user-git
RUN chmod 0440 /etc/sudoers.d/www-user-git
#USER www-user

EXPOSE 80 443
