BINARY := tun2socks
MODULE := github.com/xjasonlyu/tun2socks/v2

BUILD_DIR     := build
BUILD_TAGS    :=
BUILD_FLAGS   := -v
BUILD_COMMIT  := $(shell git rev-parse --short HEAD 2>/dev/null || echo "unknown")
BUILD_VERSION := $(shell git describe --abbrev=0 --tags HEAD 2>/dev/null || echo "unknown")

# Android 必须开启 CGO（核心是 C 代码）
CGO_ENABLED := 1
GO111MODULE := on

# 编译优化 + 版本信息注入
LDFLAGS += -w -s -buildid=
LDFLAGS += -X "$(MODULE)/internal/version.Version=$(BUILD_VERSION)"
LDFLAGS += -X "$(MODULE)/internal/version.GitCommit=$(BUILD_COMMIT)"

# 基础编译命令
GO_BUILD = GO111MODULE=$(GO111MODULE) CGO_ENABLED=$(CGO_ENABLED) \
	go build $(BUILD_FLAGS) -ldflags '$(LDFLAGS)' -tags '$(BUILD_TAGS)' -trimpath

# 仅保留 Android 架构列表（对应 NDK 的 ABI）
ANDROID_ARCH_LIST = \
	android-armeabi-v7a \
	android-arm64-v8a \
	android-x86 \
	android-x86_64

# 默认目标：编译 Android 64位核心架构
all: android-arm64-v8a

# 调试模式（仅 Android）
debug: BUILD_TAGS += debug
debug: android-all

# ====================== Android 编译核心逻辑 ======================
# 检查 NDK 环境（必选）
check-ndk:
	@if [ -z "$(ANDROID_NDK_ROOT)" ]; then \
		echo "ERROR: ANDROID_NDK_ROOT is not set"; \
		echo "       Example: export ANDROID_NDK_ROOT=/path/to/android-ndk-r25c"; \
		exit 1; \
	fi

# armeabi-v7a (32位ARM，兼容旧安卓机)
android-armeabi-v7a: check-ndk
	@echo "Building for Android armeabi-v7a..."
	export CC=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi21-clang; \
	export CXX=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi21-clang++; \
	GOARCH=arm GOARM=7 GOOS=android $(GO_BUILD) -o $(BUILD_DIR)/$(BINARY)-$@

# arm64-v8a (64位ARM，主流安卓机)
android-arm64-v8a: check-ndk
	@echo "Building for Android arm64-v8a..."
	export CC=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang; \
	export CXX=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang++; \
	GOARCH=arm64 GOOS=android $(GO_BUILD) -o $(BUILD_DIR)/$(BINARY)-$@

# x86 (32位x86，安卓模拟器/少数平板)
android-x86: check-ndk
	@echo "Building for Android x86..."
	export CC=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/i686-linux-android21-clang; \
	export CXX=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/i686-linux-android21-clang++; \
	GOARCH=386 GOOS=android $(GO_BUILD) -o $(BUILD_DIR)/$(BINARY)-$@

# x86_64 (64位x86，安卓模拟器/高端平板)
android-x86_64: check-ndk
	@echo "Building for Android x86_64..."
	export CC=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/x86_64-linux-android21-clang; \
	export CXX=$(ANDROID_NDK_ROOT)/toolchains/llvm/prebuilt/linux-x86_64/bin/x86_64-linux-android21-clang++; \
	GOARCH=amd64 GOOS=android $(GO_BUILD) -o $(BUILD_DIR)/$(BINARY)-$@

# 编译所有 Android 架构（推荐）
android-all: check-ndk $(ANDROID_ARCH_LIST)

# ====================== Android 打包逻辑 ======================
android_releases := $(addsuffix .zip, $(ANDROID_ARCH_LIST))

$(android_releases): %.zip: %
	@zip -qmj $(BUILD_DIR)/$(BINARY)-$(basename $@).zip $(BUILD_DIR)/$(BINARY)-$(basename $@)

# 打包所有 Android 产物
releases: check-ndk android-all $(android_releases)

# 清理产物
clean:
	rm -rf $(BUILD_DIR)