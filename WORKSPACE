load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

############# Frontend #######################
http_archive(
    name = "aspect_rules_js",
    sha256 = "7b2a4d1d264e105eae49a27e2e78065b23e2e45724df2251eacdd317e95bfdfd",
    strip_prefix = "rules_js-1.31.0",
    url = "https://github.com/aspect-build/rules_js/releases/download/v1.31.0/rules_js-v1.31.0.tar.gz",
)

http_archive(
    name = "aspect_rules_ts",
    sha256 = "4c3f34fff9f96ffc9c26635d8235a32a23a6797324486c7d23c1dfa477e8b451",
    strip_prefix = "rules_ts-1.4.5",
    url = "https://github.com/aspect-build/rules_ts/releases/download/v1.4.5/rules_ts-v1.4.5.tar.gz",
)

http_archive(
    name = "aspect_rules_jest",
    sha256 = "52dc08fd252add240124ef7ccc46df3a505121758dfb96578a3d5f2ebb4c2b40",
    strip_prefix = "rules_jest-0.18.1",
    url = "https://github.com/aspect-build/rules_jest/releases/download/v0.18.1/rules_jest-v0.18.1.tar.gz",
)


load("@aspect_rules_js//js:repositories.bzl", "rules_js_dependencies")

rules_js_dependencies()

load("@aspect_rules_ts//ts:repositories.bzl", "rules_ts_dependencies")

rules_ts_dependencies( ts_version_from = "//frontend:package.json" )

load("@aspect_rules_jest//jest:dependencies.bzl", "rules_jest_dependencies")

rules_jest_dependencies()

load("@rules_nodejs//nodejs:repositories.bzl", "DEFAULT_NODE_VERSION", "nodejs_register_toolchains")

nodejs_register_toolchains(
    name = "nodejs",
    node_version = DEFAULT_NODE_VERSION,
)

load("@aspect_rules_js//npm:npm_import.bzl", "npm_translate_lock")

npm_translate_lock(
    name = "npm",
    bins = {
        # derived from "bin" attribute in node_modules/next/package.json
        "next": {
            "next": "./dist/bin/next",
        },
    },
    pnpm_lock = "//frontend:pnpm-lock.yaml",
    verify_node_modules_ignored = "//:.bazelignore",
)
load("@npm//:repositories.bzl", "npm_repositories")

npm_repositories()

##################Backend#########

RULES_JVM_EXTERNAL_TAG = "4.5"
RULES_JVM_EXTERNAL_SHA = "b17d7388feb9bfa7f2fa09031b32707df529f26c91ab9e5d909eb1676badd9a6"

http_archive(
    name = "rules_jvm_external",
    strip_prefix = "rules_jvm_external-%s" % RULES_JVM_EXTERNAL_TAG,
    sha256 = RULES_JVM_EXTERNAL_SHA,
    url = "https://github.com/bazelbuild/rules_jvm_external/archive/%s.zip" % RULES_JVM_EXTERNAL_TAG,
)

load("@rules_jvm_external//:repositories.bzl", "rules_jvm_external_deps")

rules_jvm_external_deps()

load("@rules_jvm_external//:setup.bzl", "rules_jvm_external_setup")

rules_jvm_external_setup()

load("@rules_jvm_external//:defs.bzl", "maven_install")

maven_install(
    artifacts = [
        "org.springframework:spring-web:6.0.11",
        "org.springframework:spring-test:6.0.11",
        "org.springframework.boot:spring-boot-test:3.1.2",
        "org.springframework.boot:spring-boot:3.1.2",
        "org.springframework.boot:spring-boot-autoconfigure:3.1.2",
        "org.springframework.boot:spring-boot-starter-web:3.1.2",
        "org.springframework:spring-context:6.0.11",
        "org.junit.jupiter:junit-jupiter-api:5.10.0",
        "org.junit.jupiter:junit-jupiter-engine:5.10.0",
        "org.junit.platform:junit-platform-launcher:1.10.0",
        "org.junit.platform:junit-platform-reporting:1.10.0",
        "org.mockito:mockito-core:5.5.0",
        "org.mockito:mockito-junit-jupiter:5.5.0",
        "org.assertj:assertj-core:3.24.2"
    ],
    repositories = [
        # Private repositories are supported through HTTP Basic auth
        "http://username:password@localhost:8081/artifactory/my-repository",
        "https://maven.google.com",
        "https://repo1.maven.org/maven2",
    ],
)

http_archive(
    name = "contrib_rules_jvm",
    sha256 = "bd0f82def1879df85ff0a80767e6455911e1c9c1eac5db1de8f68dcccd4a3d7a",
    strip_prefix = "rules_jvm-0.18.0",
    url = "https://github.com/bazel-contrib/rules_jvm/releases/download/v0.18.0/rules_jvm-v0.18.0.tar.gz",
)

# Fetches the contrib_rules_jvm dependencies.
# If you want to have a different version of some dependency,
# you should fetch it *before* calling this.
load("@contrib_rules_jvm//:repositories.bzl", "contrib_rules_jvm_deps")

contrib_rules_jvm_deps()

# Now ensure that the downloaded deps are properly configured
load("@contrib_rules_jvm//:setup.bzl", "contrib_rules_jvm_setup")

contrib_rules_jvm_setup()

#Container Setup

http_archive(
    name = "rules_oci",
    sha256 = "fc8551ccbfe4e716c8a3876b1b42d37e80f0bbd5045ec4de3bed88f0dc1ff0aa",
    strip_prefix = "rules_oci-1.3.2",
    url = "https://github.com/bazel-contrib/rules_oci/releases/download/v1.3.2/rules_oci-v1.3.2.tar.gz",
)

load("@rules_oci//oci:dependencies.bzl", "rules_oci_dependencies")

rules_oci_dependencies()

load("@rules_oci//oci:repositories.bzl", "LATEST_CRANE_VERSION", "LATEST_ZOT_VERSION", "oci_register_toolchains")

oci_register_toolchains(
    name = "oci",
    crane_version = LATEST_CRANE_VERSION,
    # Uncommenting the zot toolchain will cause it to be used instead of crane for some tasks.
    # Note that it does not support docker-format images.
    # zot_version = LATEST_ZOT_VERSION,
)

# You can pull your base images using oci_pull like this:
load("@rules_oci//oci:pull.bzl", "oci_pull")

oci_pull(
    name = "distroless_java",
    digest = "sha256:161a1d97d592b3f1919801578c3a47c8e932071168a96267698f4b669c24c76d",
    image = "gcr.io/distroless/java17"
)

oci_pull(
    name = "distroless_nodejs",
    image = "node:latest",
    platforms = [
        "linux/amd64",
        "linux/arm64",
    ],
)


http_archive(
    name = "rules_pkg",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_pkg/releases/download/0.9.1/rules_pkg-0.9.1.tar.gz",
        "https://github.com/bazelbuild/rules_pkg/releases/download/0.9.1/rules_pkg-0.9.1.tar.gz",
    ],
    sha256 = "8f9ee2dc10c1ae514ee599a8b42ed99fa262b757058f65ad3c384289ff70c4b8",
)
load("@rules_pkg//:deps.bzl", "rules_pkg_dependencies")
rules_pkg_dependencies()

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "aspect_bazel_lib",
    sha256 = "e9505bd956da64b576c433e4e41da76540fd8b889bbd17617fe480a646b1bfb9",
    strip_prefix = "bazel-lib-1.35.0",
    url = "https://github.com/aspect-build/bazel-lib/releases/download/v1.35.0/bazel-lib-v1.35.0.tar.gz",
)

load("@aspect_bazel_lib//lib:repositories.bzl", "aspect_bazel_lib_dependencies")

aspect_bazel_lib_dependencies()