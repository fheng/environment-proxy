FROM centos:7

USER root

RUN  yum -y install rsyslog && \
	 yum clean all && \
	 echo "" > /etc/rsyslog.d/listen.conf

COPY rsyslog.conf /etc/rsyslog.conf

EXPOSE 514

CMD ["/usr/sbin/rsyslogd", "-n"]
