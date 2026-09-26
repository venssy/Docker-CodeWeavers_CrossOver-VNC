# Using Ubuntu 18.04 base image and VNC

FROM ubuntu:24.04
MAINTAINER john.shine <mr.john.shine@gmail.com>
LABEL io.openshift.expose-services="5901:tcp"

USER root

ENV DISPLAY=":1"
ENV USER="crossover"
ENV UID=1000
ENV GID=0
ENV HOME=/home/${USER}
ARG vnc_password=""
ENV TZ=Asia/Shanghai \
    LANG=zh_CN.UTF-8 \
    LANGUAGE=zh_CN:zh \
    LC_ALL=zh_CN.UTF-8

EXPOSE 5901 6080

ADD xstartup ${HOME}/.vnc/

RUN useradd -g ${GID} -u ${UID} -r -d ${HOME} -s /bin/bash ${USER}
RUN echo "root:root" | chpasswd
# set password of ${USER} to ${USER}
RUN echo "${USER}:${USER}" | chpasswd

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends apt-utils sudo git curl wget ca-certificates python3 python3-numpy fluxbox libc6:i386 libstdc++6:i386 && \
    apt-get install -y --no-install-recommends tigervnc-standalone-server tigervnc-common xvfb xterm && \

    dpkg --add-architecture i386 && \
    /bin/echo -e "\n${USER}        ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers && \

    wget https://github.com/novnc/noVNC/archive/v1.3.0.tar.gz -O /tmp/noVNC.tar.gz && \
    tar -zxvf /tmp/noVNC.tar.gz -C /opt && \
    git clone https://github.com/novnc/websockify /opt/noVNC-1.3.0/utils/websockify && \
    rm -rf /opt/noVNC-1.3.0/utils/websockify/.git /tmp/noVNC.tar.gz && \
    mv /opt/noVNC-1.3.0/vnc_lite.html /opt/noVNC-1.3.0/index.html && \
    sed -i 's/<title>noVNC<\/title>/<title>掘金量化<\/title>/g' /opt/noVNC-1.3.0/index.html && \

    wget -O /tmp/crossover-26.3.0.deb https://cpv2.mairuan.com/crossoverchina.com/trial/Mac/crossover-26.3.0.deb && \
    apt-get install -y /tmp/crossover-26.3.0.deb && \
    rm /tmp/crossover-26.3.0.deb && \

    apt-get remove -y git-man git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN locale-gen zh_CN.UTF-8 && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone

RUN touch ${HOME}/.vnc/passwd ${HOME}/.Xauthority /var/log/gm.log

RUN chown -R ${UID}:${GID} ${HOME}
RUN chown ${UID}:${GID} /var/log/gm.log
RUN chmod 755 ${HOME}/.vnc/xstartup
RUN chmod 600 ${HOME}/.vnc/passwd

WORKDIR ${HOME}

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]


USER ${USER}
WORKDIR ${HOME}

RUN wget https://gm3-static.oss-cn-shenzhen.aliyuncs.com/myquant/Goldminer3-x64-3.23.0.9.exe -O Goldminer3.exe

RUN /bin/echo -e 'alias ll="ls -last"' >> ${HOME}/.bashrc

RUN mkdir -p ${HOME}/.fluxbox
RUN /bin/echo -e "session.screen0.toolbar.placement: TopCenter" >> ${HOME}/.fluxbox/init
RUN /bin/echo -e "session.screen0.workspaces:     1 ">> ${HOME}/.fluxbox/init

# Always run the WM last!
RUN /bin/echo -e "export DISPLAY=${DISPLAY}"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "[ -r ${HOME}/.Xresources ] && xrdb ${HOME}/.Xresources\nfbsetroot -solid gray"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "fluxbox &"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e 'export GTK_IM_MODULE=fcitx' >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e 'export QT_IM_MODULE=fcitx' >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e 'export XMODIFIERS="@im=fcitx"' >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "sleep 3"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "fcitx"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "/opt/noVNC-1.3.0/utils/launch.sh --listen 6080 --vnc 127.0.0.1:5901 &"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "mkdir -p /home/crossover/gm/"  >> ${HOME}/.vnc/xstartup
RUN /bin/echo -e "chmod -R a=rwx /home/crossover/gm/"  >> ${HOME}/.vnc/xstartup

RUN /bin/echo -e "exec /opt/cxoffice/bin/crossover" >> ${HOME}/.vnc/xstartup

# RUN /bin/echo -e "while true; do" >> ${HOME}/.vnc/xstartup
# RUN /bin/echo -e "    /home/crossover/Goldminer3.exe >> /var/log/gm.log 2>&1" >> ${HOME}/.vnc/xstartup
# RUN /bin/echo -e "done" >> ${HOME}/.vnc/xstartup
