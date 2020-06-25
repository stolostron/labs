FROM fedora:31
LABEL scollier <acm-contact@redhat.com>
RUN yum -y install hostname httpd iproute python3-mod_wsgi python3 procps-ng && yum clean all
CMD [ "/sbin/httpd", "-D", "FOREGROUND" ]

