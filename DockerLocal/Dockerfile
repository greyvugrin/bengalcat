# Set the base image to ubuntu:16.04
FROM        ubuntu:16.04

# File Author / Maintainer
MAINTAINER Angela Murrell

# Update the repository and install nginx and php7.0 
RUN         apt-get update && \
            apt-get install -y nano && \
            apt-get install -y curl && \
            apt-get install -y nginx && \
            apt-get -y --no-install-recommends install php7.0-fpm && \
            apt-get clean && \
            rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN         apt-get update && \
            apt-get install -y php-curl

# export var for nano to work in command line
ENV TERM xterm

# Set workdir
WORKDIR /var/www/

# Add site directory
RUN mkdir /var/www/site

# Copy a configuration file from the current directory
ADD nginx.site.conf /etc/nginx/sites-available/

# Remove symbolic link to default enabled nginx site in sites-enabled
RUN rm /etc/nginx/sites-enabled/default

# Make Symbolic link to enable the site
RUN ln -s /etc/nginx/sites-available/nginx.site.conf /etc/nginx/sites-enabled/

# Append "daemon off;" to the configuration file
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# Copy a configuration file from the current directory
ADD php7-fpm.site.conf /etc/php/7.0/fpm/pool.d

# daemon off for php also
RUN sed -i "/;daemonize = .*/c\daemonize = no" /etc/php/7.0/fpm/php-fpm.conf && \
    sed -i "/pid = .*/c\;pid = /run/php/php7.0-fpm.pid" /etc/php/7.0/fpm/php-fpm.conf

# Remove pool.d/www.conf
RUN rm /etc/php/7.0/fpm/pool.d/www.conf

# Adjust www-data
RUN usermod -u 1000 www-data

# Expose ports
EXPOSE 80

# Set the default command to execute when creating a new container
# The following runs FPM and removes all its extraneous log output on top of what your app outputs to stdout
CMD service php7.0-fpm start && service nginx start && /usr/sbin/php-fpm7.0 -F -O 2>&1 | sed -u 's,.*: \"\(.*\)$,\1,'| sed -u 's,"$,,' 1>&1 
