FROM jenkins/inbound-agent:jdk17
USER root

CMD ["mvn"]
ENV MAVEN_HOME /opt/maven

RUN set -o errexit -o nounset \
  && echo "Adding maven user and group" \
  && groupadd --system --gid 1002 maven \
  && useradd --system --gid maven --uid 1002 --shell /bin/bash --create-home maven \
  && mkdir /home/maven/.m2 \
  && chown --recursive maven:maven /home/maven \
  \
  && echo "Symlinking root Maven cache to maven Maven cache" \
  && ln --symbolic /home/maven/.m2 /root/.m2
VOLUME /home/maven/.m2
WORKDIR /home/maven

RUN set -o errexit -o nounset \
  && apt-get update \
  && apt-get install --yes --no-install-recommends \
    unzip \
    wget \
    curl \
    \
    bzr \
    git \
    git-lfs \
    mercurial \
    openssh-client \
    subversion \
  && rm --recursive --force /var/lib/apt/lists/* \
  \
  && echo "Testing VCSes" \
  && which bzr \
  && which git \
  && which git-lfs \
  && which hg \
  && which svn

ENV MAVEN_VERSION 3.9.8

# Download SHA512 hash from Apache website (corrected URL)
RUN set -o errexit -o nounset \
  && echo "Downloading SHA512 hash" \
  && wget https://downloads.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz.sha512 -O maven.sha512

# Download Maven binary
RUN set -o errexit -o nounset \
  && echo "Downloading Maven" \
  && curl -L -o maven.tar.gz "https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz" \
  \
  && echo "Creating Maven installation directory" \
  && mkdir -p /opt/maven \
  \
  && echo "Installing Maven" \
  && tar -xzf maven.tar.gz -C /opt/maven --strip-components=1 \
  && rm maven.tar.gz \
  && ln --symbolic "/opt/maven/bin/mvn" /usr/bin/mvn \
  \
  && echo "Testing Maven installation" \
  && mvn --version

# Updated Chrome download and installation
RUN set -o errexit -o nounset \
  && echo "Downloading Chrome" \
  && wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
  && dpkg -i google-chrome-stable_current_amd64.deb || apt-get install --yes --fix-broken \
  && wget https://chromedriver.storage.googleapis.com/108.0.5359.71/chromedriver_linux64.zip \
  && unzip chromedriver_linux64.zip \
  && chmod +x chromedriver \
  && mv chromedriver /usr/bin

WORKDIR /home/jenkins
USER jenkins
