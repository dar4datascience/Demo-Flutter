FROM ubuntu:22.04

# Locale and environment setup
ENV LANG C.UTF-8
ENV ANDROID_SDK_TOOLS_VERSION=8512546
ENV ANDROID_PLATFORM_VERSION=36.0.0
ENV ANDROID_BUILD_TOOLS_VERSION=34.0.0
ENV ANDROID_HOME=/home/vscode/android-sdk-linux
ENV ANDROID_SDK_ROOT=$ANDROID_HOME
ENV FLUTTER_CHANNEL=stable
ENV FLUTTER_VERSION=3.32.0
ENV FLUTTER_HOME=/home/vscode/flutter
ENV PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$FLUTTER_HOME/bin

# Create user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000

RUN apt-get update && apt-get install -y \
    sudo openjdk-11-jdk-headless wget curl git xz-utils zip unzip libglu1-mesa \
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*

USER $USERNAME
WORKDIR /home/$USERNAME

# Install Android SDK
RUN curl -o android-sdk-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_TOOLS_VERSION}_latest.zip \
    && mkdir -p $ANDROID_HOME/cmdline-tools \
    && unzip -q android-sdk-tools.zip -d $ANDROID_HOME/cmdline-tools \
    && mv $ANDROID_HOME/cmdline-tools/cmdline-tools $ANDROID_HOME/cmdline-tools/latest \
    && rm android-sdk-tools.zip \
    && yes | sdkmanager --licenses \
    && touch $HOME/.android/repositories.cfg \
    && sdkmanager --install \
      "platform-tools" \
      "emulator" \
      "platforms;android-${ANDROID_PLATFORM_VERSION}" \
      "build-tools;${ANDROID_BUILD_TOOLS_VERSION}" \
      "cmdline-tools;latest"

# Install Flutter SDK
RUN curl -o flutter.tar.xz https://storage.googleapis.com/flutter_infra_release/releases/${FLUTTER_CHANNEL}/linux/flutter_linux_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz \
    && tar -xf flutter.tar.xz -C /home/$USERNAME \
    && rm flutter.tar.xz \
    && flutter config --android-sdk "$ANDROID_SDK_ROOT" \
    && yes | flutter doctor --android-licenses \
    && flutter config --no-analytics \
    && flutter doctor

# Optional: Create a default emulator (commented out due to resource usage)
# RUN sdkmanager "system-images;android-${ANDROID_PLATFORM_VERSION};google_apis;x86_64" \
#     && echo "no" | avdmanager create avd -n Android${ANDROID_PLATFORM_VERSION} -k "system-images;android-${ANDROID_PLATFORM_VERSION};google_apis;x86_64"

