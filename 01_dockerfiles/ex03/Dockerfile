FROM ubuntu

RUN apt-get update && \
    apt-get upgrade -y && \
	DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
	ca-certificates \
	openssh-server \
	curl \
	tzdata

# Download & Install GitLab
# Сценарий настроит Ваш сервер для поддержки репозиториев GitLab. Затем Вы можете установить актуальную версию приложения GitLab:
RUN curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN apt-get install gitlab-ce -y

# Create SSL certificate
RUN mkdir /etc/gitlab/ssl/ && chmod 700 /etc/gitlab/ssl/
RUN openssl genrsa -out /etc/gitlab/ssl/git.example.com.key 2048
RUN openssl req -new -key /etc/gitlab/ssl/git.example.com.key -out /etc/gitlab/ssl/git.example.com.csr \
	-subj "/C=RU/ST=Moscow/L=Moscow/O=21-school/OU=42/CN=git.example.com"
RUN openssl x509 -in /etc/gitlab/ssl/git.example.com.csr -out /etc/gitlab/ssl/git.example.com.crt -req -signkey /etc/gitlab/ssl/git.example.com.key -days 365

RUN echo "letsencrypt['enable'] = false" >> /etc/gitlab/gitlab.rb && \
sed -i "s/^external_url.*/external_url \'https:\/\/git.example.com\'/" /etc/gitlab/gitlab.rb

# Expose web & ssh
EXPOSE 443 80 22

ENTRYPOINT (/opt/gitlab/embedded/bin/runsvdir-start &) && \
	gitlab-ctl reconfigure && \
	service ssh start && \
	tail -f /dev/null
