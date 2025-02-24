FROM registry.cern.ch/cmsweb/wmagent-base:pypi-20230705
MAINTAINER Valentin Kuznetsov vkuznet@gmail.com

# TAG to be passed at build time through `--build-arg TAG=<WMA_TAG>`. Default: None
ARG TAG=None
ENV WMA_TAG=${TAG}
ENV WMA_USER=cmst1
ENV WMA_GROUP=zh
ENV WMA_UID=31961
ENV WMA_GID=1399
ENV WMA_ROOT_DIR=/data

# Basic WMAgent directory structure passed to all scripts through env variables:
# NOTE: Those should be static and depend only on $WMA_BASE_DIR
ENV WMA_BASE_DIR=$WMA_ROOT_DIR/srv
ENV WMA_ADMIN_DIR=$WMA_ROOT_DIR/admin/wmagent
ENV WMA_CERTS_DIR=$WMA_ROOT_DIR/certs

ENV WMA_HOSTADMIN_DIR=$WMA_ADMIN_DIR/hostadmin
ENV WMA_CURRENT_DIR=$WMA_BASE_DIR/wmagent/current
ENV WMA_INSTALL_DIR=$WMA_CURRENT_DIR/install
ENV WMA_CONFIG_DIR=$WMA_CURRENT_DIR/config
ENV WMA_MANAGE_DIR=$WMA_CONFIG_DIR/wmagent
ENV WMA_DEPLOY_DIR=/usr/local
ENV WMA_ENV_FILE=$WMA_DEPLOY_DIR/deploy/env.sh


# Setting up users and previleges
RUN groupadd -g ${WMA_GID} ${WMA_GROUP}
RUN useradd -u ${WMA_UID} -g ${WMA_GID} -m ${WMA_USER}
RUN install -o ${WMA_USER} -g ${WMA_GID} -d ${WMA_ROOT_DIR}
RUN usermod -aG mysql ${WMA_USER}
RUN rm -f /etc/mysql/mariadb.conf.d/50-server.cnf

# Add WMA_USER to sudoers
RUN echo "${WMA_USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Add all deployment needed directories
ADD bin $WMA_DEPLOY_DIR/bin
ADD etc $WMA_DEPLOY_DIR/etc

# Add install script
ADD install.sh ${WMA_ROOT_DIR}/install.sh

# Add wmagent run script
ADD run.sh ${WMA_ROOT_DIR}/run.sh

# Install the requested WMA_TAG.
RUN ${WMA_ROOT_DIR}/install.sh -v ${WMA_TAG}
RUN chown -R ${WMA_USER}:${WMA_GID} ${WMA_ROOT_DIR}

# Switch to the runtime directory and user
WORKDIR ${WMA_ROOT_DIR}
USER ${WMA_USER}
ENV USER=$WMA_USER

# Define the entrypoint. All the run.sh paramters should be passed at runtime.
ENTRYPOINT ["./run.sh"]
