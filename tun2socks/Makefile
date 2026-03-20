# 仅编译 tun2socks Android AAR 库
BUILD_DIR     := build
MODULE        := github.com/xjasonlyu/tun2socks/v2
CGO_ENABLED   := 1  # AAR 编译必须开启 CGO
GO111MODULE   := on
ANDROID_API   := 21  # 兼容 Android 5.0+

# 版本信息（可选，注入到 AAR 中）
BUILD_COMMIT  := $(shell git rev-parse --short HEAD 2>/dev/null || echo "unknown")
BUILD_VERSION := $(shell git describe --abbrev=0 --tags HEAD 2>/dev/null || echo "unknown")
LDFLAGS       := -w -s -X "$(MODULE)/internal/version.Version=$(BUILD_VERSION)" -X "$(MODULE)/internal/version.GitCommit=$(BUILD_COMMIT)"

# 检查必要环境
check-env:
	@if [ -z "$(ANDROID_NDK_ROOT)" ]; then \
		echo "ERROR: ANDROID_NDK_ROOT 未设置"; \
		echo "示例: export ANDROID_NDK_ROOT=/path/to/android-ndk-r25c"; \
		exit 1; \
	fi
	@if [ -z "$(JAVA_HOME)" ]; then \
		echo "ERROR: JAVA_HOME 未设置（编译 AAR 需要 JDK）"; \
		exit 1; \
	fi

# 安装 Go Mobile（编译 AAR 核心工具）
install-gomobile:
	@echo "安装 gomobile 工具..."
	go install golang.org/x/mobile/cmd/gomobile@latest
	go install golang.org/x/mobile/cmd/gobind@latest
	gomobile init

# 核心目标：编译 AAR 库
aar: check-env install-gomobile
	@echo "开始编译 tun2socks AAR 库（API $(ANDROID_API)）..."
	mkdir -p $(BUILD_DIR)
	# 进入 engine 目录编译（tun2socks 安卓接口层）
	cd engine && \
	CGO_ENABLED=$(CGO_ENABLED) GO111MODULE=$(GO111MODULE) \
	gomobile bind -v \
		-target android \
		-androidapi $(ANDROID_API) \
		-ldflags '$(LDFLAGS)' \
		-o ../$(BUILD_DIR)/tun2socks.aar \
		./...
	@echo "AAR 编译完成：$(BUILD_DIR)/tun2socks.aar"

# 打包 AAR 为 zip（方便分发）
release: aar
	@echo "打包 AAR 为 zip..."
	zip -qmj $(BUILD_DIR)/tun2socks-aar-$(BUILD_VERSION).zip $(BUILD_DIR)/tun2socks.aar
	@echo "打包完成：$(BUILD_DIR)/tun2socks-aar-$(BUILD_VERSION).zip"

# 清理产物
clean:
	@echo "清理编译产物..."
	rm -rf $(BUILD_DIR)
	go clean -cache

# 默认目标：编译 AAR + 打包
all: release