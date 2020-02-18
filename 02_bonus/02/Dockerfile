FROM debian
RUN apt-get update && \
    apt-get upgrade -y && \
	apt-get install -yq vim ufw sudo fail2ban portsentry apache2
RUN touch /var/log/auth.log
ENTRYPOINT service ufw start && service fail2ban start && \
	service portsentry start && service apache2 start && \
	sudo ufw allow 80 && sudo ufw allow 443 && sudo ufw allow 22 && \
	tail -f /dev/null
