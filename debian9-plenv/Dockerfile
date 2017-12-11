FROM pick-your-poison

LABEL Description="debian9-plenv"

ENV DEBIAN_FRONTEND noninteractive
ENV LANG en_AU.UTF-8
ENV LANGUAGE en_AU:en
ENV PLENV_ROOT /opt/plenv

# Pass these in as --build-arg
#ENV PERLS 5.10.1 5.14.4 5.24.1 5.26.0
ARG PERLS

# Build Hardening
ARG CFLAGS="-g -O2 -fstack-protector -Wformat -Werror=format-security"
ARG CPPFLAGS="-D_FORTIFY_SOURCE=2"
ARG CXXFLAGS="-g -O2 -fstack-protector -Wformat -Werror=format-security"
ARG LDFLAGS="-Wl,-z,relro"

# Do everything in one RUN, so we only have 1 layer
RUN set -ex ; \
# Get things up to date
    apt-get update ; \
    apt-get install -y build-essential curl git time \
# Add very commonly needed dev packages
                       libgmp3-dev libssl-dev zlib1g-dev ; \
# Set up locale
    apt-get install -y locales ; \
    sed -i -e "s/# $LANG.*/$LANG UTF-8/" /etc/locale.gen ; \
    dpkg-reconfigure locales ; \
    update-locale LANG=$LANG ; \
# Clone stuff we need
    git clone --depth 1 git://github.com/tokuhirom/plenv.git $PLENV_ROOT ; \
    git clone --depth 1 git://github.com/tokuhirom/Perl-Build.git $PLENV_ROOT/plugins/perl-build/ ; \
# Add /build dir
    mkdir /build ; \
# Add plenv environment to global profile
    echo "export PLENV_ROOT=\"$PLENV_ROOT\"\nexport PATH=\"\$PLENV_ROOT/bin:\$PATH\"\neval \"\$(plenv init -)\"" >> /etc/profile.d/plenv.sh ; \
    chmod 755 /etc/profile.d/plenv.sh ; \
# Build perls
    . /etc/profile; \
    for ver in $PERLS; do \
        time plenv install \
            -D usethreads -D usemultiplicity -D useshrplib \
            -D man1dir=none -D man3dir=none \
            $ver ; \
        plenv global $ver ; \
        plenv install-cpanm ; \
        plenv rehash ; \
# Install some super common modules
        cpanm Module::Build ; \
        cpanm Net::SSLeay ; \
        cpanm Math::GMP ; \
        cpanm DBI ; \
        cpanm Try::Tiny ; \
        cpanm LWP::Protocol::https ; \
        cpanm JSON::MaybeXS ; \
    done ; \
# Clean up after ourselves
    plenv global system ; \
    apt-get clean ; \
    rm -rfv $PLENV_ROOT/build/* ; \
    rm -rf ~/.cpanm/* ; \
    rm -rf /tmp/*

CMD ["/bin/bash", "-l"]
