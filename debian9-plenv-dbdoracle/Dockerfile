FROM debian9-plenv:5.26.1

LABEL Description="debian9-plenv-dbdoracle"

ENV DEBIAN_FRONTEND noninteractive

# We want to use full shell expansions
SHELL [ "/bin/bash", "-c" ]

# Build Hardening
ARG CFLAGS="-g -O2 -fstack-protector -Wformat -Werror=format-security"
ARG CPPFLAGS="-D_FORTIFY_SOURCE=2"
ARG CXXFLAGS="-g -O2 -fstack-protector -Wformat -Werror=format-security"
ARG LDFLAGS="-Wl,-z,relro"

# Insert the ZIP files (this creates another layer, sorry)
ADD oracle/*zip /usr/lib/oracle/

# Do everything in one RUN, so we only have 1 layer
RUN set -ex ; \
# Get things up to date
    apt-get update ; \
    apt-get install -y unzip libaio1; \
# Unzip oracle and move things around
    mkdir /etc/oracle ; \
    mkdir -p /usr/lib/oracle/11.2/client/bin ; \
    mkdir -p /usr/lib/oracle/11.2/client/lib ; \
    mkdir -p /usr/include/oracle/11.2/client ; \
    mkdir -p /usr/share/oracle/11.2/client ; \
    cd /usr/lib/oracle ; \
    unzip instantclient-basic-linux.x64-11.2.0.4.0.zip ; \
    unzip instantclient-sqlplus-linux.x64-11.2.0.4.0.zip ; \
    unzip instantclient-sdk-linux.x64-11.2.0.4.0.zip ; \
# Contents of instantclient-basic-linux.x64-11.2.0.4.0.zip
    mv instantclient_11_2/adrci 11.2/client/bin/ ; \
    mv instantclient_11_2/genezi 11.2/client/bin/ ; \
    mv instantclient_11_2/{libclntsh.so.11.1,libnnz11.so,libocci.so.11.1,libociei.so,libocijdbc11.so,ojdbc5.jar,ojdbc6.jar,xstreams.jar} 11.2/client/lib/ ; \
# Contents of instantclient-sqlplus-linux.x64-11.2.0.4.0.zip
    mv instantclient_11_2/sqlplus 11.2/client/bin/ ; \
    mv instantclient_11_2/glogin.sql instantclient_11_2/libsqlplus.so instantclient_11_2/libsqlplusic.so 11.2/client/lib/ ; \
# Contents of instantclient-sdk-linux.x64-11.2.0.4.0.zip
    mv instantclient_11_2/sdk/include/*h /usr/include/oracle/11.2/client/ ; \
    mv instantclient_11_2/sdk/demo/* /usr/share/oracle/11.2/client/ ; \
    mv instantclient_11_2/sdk/ott /usr/share/oracle/11.2/client/ ; \
    mv instantclient_11_2/sdk/ottclasses.zip 11.2/client/lib/ottclasses.zip ; \
    ln -s libclntsh.so.11.1 11.2/client/lib/libclntsh.so ; \
    ln -s libocci.so.11.1 11.2/client/lib/libocci.so ; \
# Place paths in ENV
    echo "export ORACLE_HOME=/usr/lib/oracle/11.2/client" >> /etc/profile.d/oracle.sh ; \
    echo "export PATH=\$PATH:\$ORACLE_HOME/bin" >> /etc/profile.d/oracle.sh ; \
    echo "export TNS_ADMIN=/etc/oracle" >> /etc/profile.d/oracle.sh ; \
    echo "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:\$ORACLE_HOME/lib" >> /etc/profile.d/oracle.sh ; \
    . /etc/profile ; \
# Make sure stuff is +x
    chmod +x 11.2/client/bin/* ; \
# Clean up
    rm -rf instantclient_11_2 ; \
    cd / ; \
# Finished with Oracle Client
# Now install DBD::Oracle
    plenv global 5.26.1 ; \
    cpanm -v DBD::Oracle ; \
# Finished with DBD::Oracle
# Clean up after ourselves
    plenv global system ; \
    apt-get remove -y --purge unzip ; \
    apt-get clean ; \
    rm -rf ~/.cpanm/* ; \
    rm -rf /tmp/*

CMD ["/bin/bash", "-l"]
