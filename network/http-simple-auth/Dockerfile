FROM            nginx:alpine
MAINTAINER      Eugene Cheah <eugene@picoded.com>

# Makes only the entry point, no other points needed
RUN mkdir /entrypoint;

# Port to expose, this is currently fixed to 80
EXPOSE 80

#
# The htpasswd file configuration.
# Default is "picoded:{PLAIN}isAwesome!", which is unencrypted. You may overwrite with encrypted htpasswd
#
# You can use either an offline httpasswd generator (for the peneroid)
# Or an online conguration generator such as : http://aspirine.org/htpasswd_en.html
#
# Note, if you are configuring this variable via command line, you will want to use
# single quotes, to ensure the $ signs in the hashed password.
#
# You may use multiple lines, via \n "newline"
#
ENV HTPASSWD="picoded:{PLAIN}isAwesome!"

#
# Server host to make request to, 
# you may use a named container of "webhost" instead
#
ENV FORWARD_HOST webhost

# The destination server port
ENV FORWARD_PORT 80

# Authentication realm, used by auth_basic (see: http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html)
ENV AUTH_REALM="Restricted"

# Nginx proxy read timed, default is 600 seconds (10 minutes)
ENV PROXY_READ_TIMEOUT 600

#
# Prepares the entrypoint primer script, and runs it once
#  
RUN echo '#!/bin/sh'                                                                      > /entrypoint/primer.sh && \
	echo ''                                                                                >> /entrypoint/primer.sh && \
	echo '# Writes and setup the password'                                                 >> /entrypoint/primer.sh && \
	echo 'echo "${HTPASSWD}" > /etc/nginx/auth.htpasswd'                                   >> /entrypoint/primer.sh && \
	echo ''                                                                                >> /entrypoint/primer.sh && \
	echo '# Goes into the nginx config folder'                                             >> /entrypoint/primer.sh && \
	echo 'cd /etc/nginx/conf.d/'                                                           >> /entrypoint/primer.sh && \
	echo ''                                                                                >> /entrypoint/primer.sh && \
	echo '# Setup the server config'                                                       >> /entrypoint/primer.sh && \
	echo 'echo "server {"                                                 > default.conf'  >> /entrypoint/primer.sh && \
	echo 'echo "   listen 80 default_server;"                             >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo ""                                                         >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "   location / {"                                          >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "      auth_basic            \"${AUTH_REALM}\";"           >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "      auth_basic_user_file  auth.htpasswd;"               >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo ""                                                         >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "      proxy_pass http://${FORWARD_HOST}:${FORWARD_PORT};" >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "      proxy_read_timeout    ${PROXY_READ_TIMEOUT};"       >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "      proxy_pass_request_headers    on;"                  >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "   }"                                                     >> default.conf' >> /entrypoint/primer.sh && \
	echo 'echo "}"                                                        >> default.conf' >> /entrypoint/primer.sh && \
	echo ''                                                                                >> /entrypoint/primer.sh && \
	echo '# Goes back to root folder'                                                      >> /entrypoint/primer.sh && \
	echo 'cd /'                                                                            >> /entrypoint/primer.sh && \
	echo ''                                                                                >> /entrypoint/primer.sh && \
	echo '# Chain the execution commands'                                                  >> /entrypoint/primer.sh && \
	echo 'exec "$@"'                                                                       >> /entrypoint/primer.sh && \
	chmod +x /entrypoint/primer.sh && \
	/entrypoint/primer.sh;

#
# Usef  ul for debugging
#
# RUN cat /etc/nginx/conf.d/default.conf;
# RUN cat /entrypoint/primer.sh;

ENTRYPOINT ["/entrypoint/primer.sh"]
CMD nginx -g "daemon off;"
