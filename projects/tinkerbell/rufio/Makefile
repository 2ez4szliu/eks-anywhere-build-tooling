BASE_DIRECTORY:=$(abspath ../../../)
GIT_TAG=$(shell cat GIT_TAG)
GOLANG_VERSION=$(shell cat GOLANG_VERSION)
REPO=rufio
REPO_OWNER=tinkerbell

BASE_IMAGE_NAME?=eks-distro-minimal-base-glibc

BINARY_TARGET_FILES=manager
SOURCE_PATTERNS=.

HAS_S3_ARTIFACTS=true

# we need to set IMAGE_BUILD_ARGS here even though its the same as the default. 
# it is set in Common.mk on the images target (https://github.com/aws/eks-anywhere-build-tooling/blob/8b6d6d66974e881b22e3c9c8ea29adc26f7df5fd/Common.mk#L799)
# and the combine-images target (https://github.com/aws/eks-anywhere-build-tooling/blob/8b6d6d66974e881b22e3c9c8ea29adc26f7df5fd/Common.mk#L846)
# since combine-images has images as prereq target, the ?= does not really behave as one might expect.
# the images target being the actual action, its version of the set takes prioirty and resets to empty
# setting it explicitly to empty here takes allows the combine-images override to take proirty
IMAGE_BUILD_ARGS=
DOCKERFILE_FOLDER=./docker/linux
BUILDSPECS=buildspec.yml buildspecs/combine-images.yml
BUILDSPEC_1_DEPENDS_ON_OVERRIDE=none
BUILDSPEC_1_VARS_KEYS=IMAGE_PLATFORMS
BUILDSPEC_1_VARS_VALUES=IMAGE_PLATFORMS
BUILDSPEC_1_ARCH_TYPES=LINUX_CONTAINER ARM_CONTAINER
BUILDSPEC_2_DEPENDS_ON_OVERRIDE=tinkerbell_rufio_linux_amd64 tinkerbell_rufio_linux_arm64

include $(BASE_DIRECTORY)/Common.mk

s3-artifacts: create-manifests

.PHONY: create-manifests
create-manifests: tarballs | ensure-yq $$(ENABLE_DOCKER)
	@build/create_manifests.sh $(REPO) $(OUTPUT_DIR) $(ARTIFACTS_PATH) $(GIT_TAG) $(GOLANG_VERSION)

FIX_LICENSE_TARGET=$(REPO)/vendor/github.com/bmc-toolbox/common/LICENSE

# Ensure we download the license for the bmc-toolbox/common dependency ahead of gathering licenses.
$(GATHER_LICENSES_TARGETS): | $(FIX_LICENSE_TARGET)

$(FIX_LICENSE_TARGET): | $(GO_MOD_DOWNLOAD_TARGETS)
	dest=$(REPO)/vendor/github.com/bmc-toolbox/common/LICENSE; \
	mkdir -p $$(dirname $$dest); \
	wget -q --retry-connrefused -O $$dest \
		https://raw.githubusercontent.com/bmc-toolbox/common/main/LICENSE;


########### DO NOT EDIT #############################
# To update call: make add-generated-help-block
# This is added to help document dynamic targets and support shell autocompletion
# Run make help for a formatted help block with all targets
include Help.mk
########### END GENERATED ###########################
