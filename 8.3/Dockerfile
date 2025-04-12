FROM magento/magento-cloud-docker-php:8.3-cli-1.4.2

ENV MAGENTO_ROOT=/var/www/html

ADD bin/* /usr/local/bin/

RUN chown -R www:www /usr/local

RUN ["chmod", "+x", \
    "/usr/local/bin/magento-worker-daemon" \
]

WORKDIR ${MAGENTO_ROOT}

USER www

CMD ["magento-worker-daemon"]