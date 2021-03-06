
##################################################
#                                                #
# DO NOT EDIT THIS FILE MANUALLY                 #
# AUTOMATICALLY CREATED WITH docker-builder.sh   #
#                                                #
##################################################
  

FROM whatwedo/base:latest
RUN apt-get update -y && \
apt-get update && apt-get install -y openssh-server && \
mkdir -p /var/run/sshd && \
echo "UseDNS no" >> /etc/ssh/sshd_config
COPY files/supervisord/sshd.conf /etc/supervisor/conf.d/sshd.conf
RUN apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0x7BD9BF62 && \
add-apt-repository 'deb http://nginx.org/packages/mainline/ubuntu trusty nginx' && \
apt-get update && \
apt-get install -y nginx && \
touch /var/log/nginx/access.log && \
touch /var/log/nginx/error.log && \
mkdir /var/www && \
chown -R www-data:www-data /var/www/ && \
chmod -R 2755 /var/www/ && \
rm -rf /etc/nginx/sites-available/ && \
rm -rf /etc/nginx/sites-enabled/ && \
rm -rf /etc/nginx/conf.d/ && \
rm /etc/nginx/nginx.conf
ADD files/nginx-default /etc/nginx
RUN chown -R www-data /var/www/ && \
chmod -R 755 /var/www/
COPY files/supervisord/nginx.conf /etc/supervisor/conf.d/nginx.conf
RUN apt-get install -y build-essential libssl-dev libcurl4-gnutls-dev libexpat1-dev gettext && \
mkdir /tmp/git && \
cd /tmp/git && \
curl -L --progress https://github.com/git/git/archive/v2.9.0.tar.gz -o git.tar.gz && \
echo '382d0446b4fdbd9e6fd1e474e535937e8d5446b5  git.tar.gz' | shasum -c - && \
tar xzf git.tar.gz && \
cd /tmp/git/git-* && \
make prefix=/usr all && \
make prefix=/usr install
RUN apt-get purge -y libssl-dev libcurl4-gnutls-dev libexpat1-dev && \
apt-get update && apt-get install --no-install-recommends -y \
ca-certificates \
curl \
mercurial \
git-core
RUN mkdir /tmp/go && \
cd /tmp/go && \
curl -L --progress https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz -o go.tar.gz && \
echo 'b8318b09de06076d5397e6ec18ebef3b45cd315d  go.tar.gz' | shasum -c - && \
tar -C /usr/local -xzf go.tar.gz
ENV GOPATH /go
ENV GOROOT /usr/local/go
ENV PATH $PATH:/usr/local/go/bin:/go/bin
RUN apt-get update && apt-get install -y \
build-essential zlib1g-dev libyaml-dev libssl-dev libgdbm-dev \
libreadline-dev libncurses5-dev libffi-dev curl openssh-server \
checkinstall libxml2-dev libxslt-dev libcurl4-openssl-dev libicu-dev \
logrotate python-docutils pkg-config cmake nodejs libmysqlclient-dev \
mysql-client redis-tools
RUN mkdir /tmp/ruby && \
cd /tmp/ruby && \
curl -O --progress https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz && \
echo 'c39b4001f7acb4e334cb60a0f4df72d434bef711  ruby-2.3.1.tar.gz' | shasum -c - && tar xzf ruby-2.3.1.tar.gz && \
cd ruby-2.3.1 && ./configure --disable-install-rdoc && make && make install
RUN gem install bundler --no-ri --no-rdoc && \
adduser --disabled-login --gecos 'GitLab' git && \
sudo -u git -H mkdir -p /home/git/.ssh && \
sudo -u git -H touch /home/git/.ssh/authorized_keys && \
sudo -u git -H git config --global core.autocrlf "input" && \
sudo -u git -H git config --global gc.auto 0 && \
sudo -u git -H curl -L https://github.com/gitlabhq/gitlabhq/archive/v8.11.6.zip -o /home/git/gitlab.zip && \
sudo -u git -H unzip /home/git/gitlab.zip -d /home/git && \
sudo -u git -H mv /home/git/gitlabhq-* /home/git/gitlab && \
sudo -u git -H rm /home/git/gitlab.zip
ADD files/gitlab/gitlab.yml /home/git/gitlab/config/
RUN chown git /home/git/gitlab/config/ && \
sudo -u git -H cp /home/git/gitlab/config/secrets.yml.example /home/git/gitlab/config/secrets.yml && \
sudo -u git -H chmod 0600 /home/git/gitlab/config/secrets.yml && \
chown -R git /home/git/gitlab/log/ && \
chown -R git /home/git/gitlab/tmp/ && \
chmod -R u+rwX,go-w /home/git/gitlab/log/ && \
chmod -R u+rwX /home/git/gitlab/tmp/ && \
sudo -u git -H mkdir -p /home/git/gitlab/tmp/pid && \
sudo -u git -H mkdir -p /home/git/gitlab/tmp/sockets && \
sudo -u git -H mkdir -p /home/git/gitlab/tmp/backups && \
sudo -u git -H mkdir -p /home/git/gitlab/tmp/sessions && \
chmod -R u+rwX /home/git/gitlab/tmp/pids/ && \
chmod -R u+rwX /home/git/gitlab/tmp/sockets/ && \
chmod -R u+rwX /home/git/gitlab/tmp/backups/ && \
chmod -R u+rwX /home/git/gitlab/tmp/sessions/ && \
chmod -R u+rwX /home/git/gitlab/builds/ && \
chmod -R u+rwX /home/git/gitlab/shared/artifacts/ && \
sudo -u git -H cp /home/git/gitlab/config/unicorn.rb.example /home/git/gitlab/config/unicorn.rb && \
sudo -u git -H cp /home/git/gitlab/config/initializers/rack_attack.rb.example /home/git/gitlab/config/initializers/rack_attack.rb && \
sudo -u git -H cp /home/git/gitlab/config/resque.yml.example /home/git/gitlab/config/resque.yml && \
sudo -u git -H cp /home/git/gitlab/config/database.yml.mysql /home/git/gitlab/config/database.yml && \
sudo -u git -H chmod o-rwx /home/git/gitlab/config/database.yml && \
echo 'PATH="/home/git/gitlab-workhorse:$PATH"' >> /home/git/.profile
RUN cd /home/git/gitlab && sudo -u git -H bundle install --deployment --without development test postgres aws kerberos && \
rm /etc/nginx/nginx.conf
COPY files/gitlab/nginx.conf /etc/nginx/nginx.conf
RUN sudo -u git -H git clone https://gitlab.com/gitlab-org/gitlab-workhorse.git /home/git/gitlab-workhorse && \
cd /home/git/gitlab-workhorse && sudo -u git -H git checkout v$(cat /home/git/gitlab/GITLAB_WORKHORSE_VERSION) && \
cd /home/git/gitlab-workhorse && PATH=$PATH:/usr/local/go/bin:/go/bin make && chown -R git:git /home/git/gitlab-workhorse && \
ln -s /home/git/gitlab-workhorse/gitlab-zip-cat /usr/local/bin/gitlab-zip-cat
RUN ln -s /home/git/gitlab-workhorse/gitlab-zip-metadata /usr/local/bin/gitlab-zip-metadata && \
echo 'echo "checking data directories"' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/artifacts' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/lfs/lfs-objects' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/ci/builds/' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/shared' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/gitlab-satellites' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/backups' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/repositories' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/gitlab-shell/hooks' >> /bin/everyboot && \
echo 'sudo mkdir -p /data/public/uploads' >> /bin/everyboot && \
echo 'chown -R git:git /data' >> /bin/everyboot && \
sudo -u git -H mkdir -p /home/git/.ssh && \
echo 'chown -R git:git /home/git/.ssh' >> /bin/everyboot && \
sudo -u git -H touch /home/git/.ssh/authorized_keys && \
echo 'echo "symlink uploads directory"' >> /bin/everyboot && \
echo 'rm -rf /home/git/gitlab/public/uploads' >> /bin/everyboot && \
echo 'sudo -u git -H ln -sf /data/public/uploads /home/git/gitlab/public/uploads' >> /bin/everyboot && \
echo 'cd /home/git/gitlab' >> /bin/everyboot && \
echo 'echo "configuring GitLab"' >> /bin/everyboot && \
echo 'sed -i s/^worker_processes.*/worker_processes\ ${UNICORN_WORKER_PROCESSES}/g config/unicorn.rb' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_HOST}}/${GITLAB_HOST}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PORT}}/${GITLAB_PORT}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_HTTPS}}/${GITLAB_HTTPS}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_EMAIL_FROM}}/${GITLAB_EMAIL_FROM}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_EMAIL_DISPLAY_NAME}}/${GITLAB_EMAIL_DISPLAY_NAME}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_EMAIL_REPLY_TO}}/${GITLAB_EMAIL_REPLY_TO}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_DEFAULT_THEME}}/${GITLAB_DEFAULT_THEME}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_ISSUES}}/${GITLAB_PROJECT_FEATURES_ISSUES}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_MERGE_REQUEST}}/${GITLAB_PROJECT_FEATURES_MERGE_REQUEST}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_WIKI}}/${GITLAB_PROJECT_FEATURES_WIKI}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_SNIPPETS}}/${GITLAB_PROJECT_FEATURES_SNIPPETS}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_BUILDS}}/${GITLAB_PROJECT_FEATURES_BUILDS}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_PROJECT_FEATURES_CONTAINER_REGISTRY}}/${GITLAB_PROJECT_FEATURES_CONTAINER_REGISTRY}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_BACKUP_KEEP_TIME}}/${GITLAB_BACKUP_KEEP_TIME}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_ENABLED}}/${GITLAB_INCOMING_EMAIL_ENABLED}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_ADDRESS}}/${GITLAB_INCOMING_EMAIL_ADDRESS}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_USER}}/${GITLAB_INCOMING_EMAIL_USER}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_PASSWORD}}/${GITLAB_INCOMING_EMAIL_PASSWORD}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_IMAP_HOST}}/${GITLAB_INCOMING_EMAIL_IMAP_HOST}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_IMAP_PORT}}/${GITLAB_INCOMING_EMAIL_IMAP_PORT}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_IMAP_SSL}}/${GITLAB_INCOMING_EMAIL_IMAP_SSL}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_IMAP_STARTTLS}}/${GITLAB_INCOMING_EMAIL_IMAP_STARTTLS}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{GITLAB_INCOMING_EMAIL_IMAP_MAILBOX}}/${GITLAB_INCOMING_EMAIL_IMAP_MAILBOX}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{CONTAINER_REGISTRY_HOST}}/${CONTAINER_REGISTRY_HOST}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{CONTAINER_REGISTRY_PORT}}/${CONTAINER_REGISTRY_PORT}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s@{{CONTAINER_REGISTRY_API_URL}}@${CONTAINER_REGISTRY_API_URL}@g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/{{CONTAINER_REGISTRY_ISSUER}}/${CONTAINER_REGISTRY_ISSUER}/g config/gitlab.yml' >> /bin/everyboot && \
echo 'sed -i s/#\ db_key_base\:$/db_key_base:\ ${GITLAB_DATABASE_SECRET_KEY}/g config/secrets.yml' >> /bin/everyboot && \
echo 'sed -i s@url\:.*@url\:\ ${REDIS_URL}@g config/resque.yml' >> /bin/everyboot && \
echo 'sed -i s/username\:.*/username\:\ ${DATABASE_USER}/g config/database.yml' >> /bin/everyboot && \
echo 'sed -i s/password\:.*/password\:\ "${DATABASE_PASSWORD}"/g config/database.yml' >> /bin/everyboot && \
echo 'sed -i s/database\:.*/database\:\ ${DATABASE_NAME}/g config/database.yml' >> /bin/everyboot && \
echo 'sed -i s/#\ host\:.*/host\:\ ${DATABASE_HOST}/g config/database.yml' >> /bin/everyboot && \
echo 'sed -i s/config.action_mailer.delivery_method\ =\ :sendmail/config.action_mailer.delivery_method\ =\ :smtp/g config/environments/production.rb' >> /bin/everyboot && \
echo 'cp config/initializers/smtp_settings.rb.sample config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/address\:.*/address\:\ \"${GITLAB_EMAIL_SMTP_ADDRESS}\",/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/port\:.*/port\:\ ${GITLAB_EMAIL_SMTP_PORT},/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/authentication\:.*/authentication\:\ \:${GITLAB_EMAIL_SMTP_AUTHENTICATION},/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/user_name\:.*/user_name\:\ \"${GITLAB_EMAIL_SMTP_USERNAME}\",/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/password\:.*/password\:\ \"${GITLAB_EMAIL_SMTP_PASSWORD}\",/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/domain\:.*/domain\:\ \"${GITLAB_EMAIL_SMTP_DOMAIN}\",/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'sed -i s/openssl_verify_mode\:.*/openssl_verify_mode\:\ \"${GITLAB_EMAIL_SMTP_VERIFY_MODE}\",\\\\n\ \ \ \ tls:\ true,/g config/initializers/smtp_settings.rb' >> /bin/everyboot && \
echo 'echo "Wait for MySQL to boot..."' >> /bin/everyboot && \
echo 'echo "while ! nc -z ${DATABASE_HOST} 3306; do sleep 3; done"' >> /bin/everyboot && \
echo 'echo "Installing GitLab Shell"' >> /bin/everyboot && \
echo 'sudo -u git -H bundle exec rake gitlab:shell:install REDIS_URL=${REDIS_URL} RAILS_ENV=production' >> /bin/everyboot && \
echo 'sed -i s@gitlab_url\:.*@gitlab_url\:\ http\://127.0.0.1\:8080@g /home/git/gitlab-shell/config.yml' >> /bin/everyboot && \
echo 'if [ ! -f /etc/firstboot/gitlab-flag ];' >> /bin/everyboot && \
echo 'then' >> /bin/everyboot && \
echo '  echo "Creating SSH Keys"' >> /bin/everyboot && \
echo '  echo -e "y\n"|sudo -u git -H ssh-keygen -q -t rsa -N "" -f /home/git/.ssh/id_rsa' >> /bin/everyboot && \
echo '  echo "Installing GitLab"' >> /bin/everyboot && \
echo '  sudo -u git -H force=yes bundle exec rake gitlab:setup RAILS_ENV=production GITLAB_ROOT_PASSWORD=${GITLAB_ROOT_PASSWORD}' >> /bin/everyboot && \
echo '  echo "Installing Backup CronJob"' >> /bin/everyboot && \
echo '  sudo -u git -H crontab -l > /home/git/gitlab/tmp/cron' >> /bin/everyboot && \
echo '  echo "${GITLAB_BACKUP_CRON} cd /home/git/gitlab && bundle exec rake gitlab:backup:create RAILS_ENV=production" >> /home/git/gitlab/tmp/cron' >> /bin/everyboot && \
echo '  sudo -u git -H crontab /home/git/gitlab/tmp/cron' >> /bin/everyboot && \
echo '  sudo -u git -H rm /home/git/gitlab/tmp/cron' >> /bin/everyboot && \
echo '  date > /etc/firstboot/gitlab-flag' >> /bin/everyboot && \
echo 'chmod 600 /home/git/.ssh/id_rsa' >> /bin/everyboot && \
echo 'chmod 644 /home/git/.ssh/id_rsa.pub' >> /bin/everyboot && \
echo '  echo ""' >> /bin/everyboot && \
echo '  echo "#########################"' >> /bin/everyboot && \
echo '  echo "# GitLab Setup finished #"' >> /bin/everyboot && \
echo '  echo "#########################"' >> /bin/everyboot && \
echo '  echo ""' >> /bin/everyboot && \
echo 'fi' >> /bin/everyboot && \
echo 'echo "Migrate database"' >> /bin/everyboot && \
echo 'sudo -u git -H bundle exec rake db:migrate RAILS_ENV=production' >> /bin/everyboot && \
echo 'echo "Precompiling assets in the background"' >> /bin/everyboot && \
echo 'sudo -u git -H bundle exec rake assets:precompile RAILS_ENV=production &' >> /bin/everyboot && \
echo 'echo "Rebuild authorized_keys file"' >> /bin/everyboot && \
echo 'sudo -u git -H bundle exec rake gitlab:shell:setup RAILS_ENV=production force=yes' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/gitlab-workhorse.log' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/mail_room.log' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/production.log' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/sidekiq.log' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/unicorn.stderr.log' >> /bin/everyboot && \
echo 'touch /home/git/gitlab/log/unicorn.stdout.log' >> /bin/everyboot && \
echo 'chown -R git:git /home/git/gitlab/log' >> /bin/everyboot && \
echo 'echo ""' >> /bin/everyboot && \
echo 'echo "######################"' >> /bin/everyboot && \
echo 'echo "# GitLab initialized #"' >> /bin/everyboot && \
echo 'echo "######################"' >> /bin/everyboot
RUN echo 'echo ""' >> /bin/everyboot
COPY files/supervisord/gitlab.conf /etc/supervisor/conf.d/gitlab.conf
RUN apt-get autoremove -y && \
apt-get clean -y && \
rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
EXPOSE 22
EXPOSE 80
VOLUME /data
VOLUME /home/git/.ssh
LABEL ch.whatwedo.image.base="whatwedo/gitlab"
