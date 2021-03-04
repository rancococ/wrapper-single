# from registry.cn-hangzhou.aliyuncs.com/rancococ/oraclejre:1.8.0_192.6-alpine as builder
FROM registry.cn-hangzhou.aliyuncs.com/rancococ/oraclejre:1.8.0_192.6-alpine as builder

# maintainer
MAINTAINER "rancococ" <rancococ@qq.com>

# set arg info
ARG jre_version=1.8.192
ARG wrapper_version=3.5.43.7

# copy script
COPY wrapper-single/ /build/wrapper-single/

# install wrapper-single
RUN sed -i 's/^set.JAVA_HOME/#&/g' "/build/wrapper-single/conf/wrapper.conf"      && \
    \rm -rf /build/wrapper-single/bin/*.bat                                       && \
    \rm -rf /build/wrapper-single/bin/*.exe                                       && \
    \rm -rf /build/wrapper-single/conf/wrapper-property.conf                      && \
    \rm -rf /build/wrapper-single/conf/wrapper-additional.conf                    && \
    \rm -rf /build/wrapper-single/libcore/*.dll                                   && \
    \rm -rf /build/wrapper-single/libextend/*.dll                                 && \
    \rm -rf /build/wrapper-single/tool                                            && \
    touch /build/wrapper-single/bin/version                                       && \
    echo "jre:${jre_version}"         >> /build/wrapper-single/bin/version        && \
    echo "wrapper:${wrapper_version}" >> /build/wrapper-single/bin/version        && \
    find /build/wrapper-single -exec touch {} \;                                  && \
    find /build/wrapper-single -type d -exec chmod 755 {} \;                      && \
    find /build/wrapper-single -type f -exec chmod 644 {} \;                      && \
    find /build/wrapper-single -type f -name ".keep" -exec rm -rf {} \;           && \
    find /build/wrapper-single -type f -name ".gitignore" -exec rm -rf {} \;      && \
    chmod 744 /build/wrapper-single/bin/*                                         && \
    chmod 644 /build/wrapper-single/bin/*.jar                                     && \
    chmod 644 /build/wrapper-single/bin/*.cnf                                     && \
    chmod 644 /build/wrapper-single/bin/version                                   && \
    chmod 600 /build/wrapper-single/conf/*.password                               && \
    chmod 777 /build/wrapper-single/logs                                          && \
    chmod 777 /build/wrapper-single/temp                                          && \
    chown -R app:app /build/wrapper-single                                        && \
    echo "script executed successfully."

# from registry.cn-hangzhou.aliyuncs.com/rancococ/oraclejre:1.8.0_192.6-alpine
FROM registry.cn-hangzhou.aliyuncs.com/rancococ/oraclejre:1.8.0_192.6-alpine

# copy from builder
COPY --from=builder /build/wrapper-single/ /data/app/

# install wrapper-single
RUN /data/app/bin/wrapper-create-linkfile.sh                                      && \
    \ln -s /data/app/bin/docker-preprocess.sh /docker-preprocess.sh               && \
    chmod +x /docker-preprocess.sh                                                && \
    echo "script executed successfully."

# set work home
WORKDIR /data/app

# expose port
EXPOSE 9404 8080 10087 10001 10002

# stop signal
STOPSIGNAL SIGTERM

# entry point
ENTRYPOINT ["/docker-entrypoint.sh"]

# default command
CMD ["/data/app/bin/wrapper-linux-x86-64", "/data/app/conf/wrapper.conf", "wrapper.syslog.ident=myapp", "wrapper.name=myapp", "wrapper.displayname=myapp", "wrapper.pidfile=/data/app/bin/myapp.pid", "wrapper.statusfile=/data/app/bin/myapp.status", "wrapper.java.pidfile=/data/app/bin/myapp.java.pid", "wrapper.java.idfile=/data/app/bin/myapp.java.id", "wrapper.java.statusfile=/data/app/bin/myapp.java.status", "wrapper.script.version=3.5.43"]
