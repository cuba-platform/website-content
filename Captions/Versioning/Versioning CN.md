# 稳定发行的版本号说明
# Stable Release Versioning
CUBA平台的稳定发行版本号遵循下列标准语义编号 - 主要.次要.维护, 例如： 7.2.4
主要版本 "7"
次要版本 "2"
维护版本 "4"
CUBA Platform follows a standard semantic numeration for its stable versions - major.minor.maintenance, e.g. 7.2.4
Major version "7"
Minor version "2"
Maintenance version "4"

## 维护版本
## MAINTENANCE RELEASE
维护版本是向后兼容的，主要包含错误修正，微小的改进，针对关键性能或安全问题的修正。 极少数情况下，可能会包括一些微小功能或API优化，但不会带来破坏性变化。
A maintenance release is backwards compatible, bringing mostly bugfixes, minor improvements, fixes for critical performance and security issues. In rare cases, it may include some minor features or API enhancements, which do not introduce breaking changes.

## 次要版本
## MINOR RELEASE
次要版本大部分是向后兼容的，但是，在功能和API层面可能包含一些重大变化。所有的重大变化以及对应的升级方案会列在 [发行说明（修改历史）](https://www.cuba-platform.cn/download/previous-platform/) 中。通常，在通过Studio进行CUBA版本升级后，CUBA studio会自动应用所需的代码改动。次要版本主要用于引入新功能，同时保持快速、轻松的升级过程。
Often, the required modifications in the source code and application configurations are applied automatically by CUBA Studio after a version upgrade. A minor release is intended to introduce new features, meanwhile keeping upgrade procedure fast and easy.

## MAJOR UPDATE
## 主要版本与重大更新
主要版本用于引入最新流行的开发范例、方法以及体系架构和功能的重大改进。通常，会在核心体系架构、功能特性、应用程序级API、基础库及其版本上带来不兼容的更改。重大更新不保证能向后兼容，在新的重大更新中，旧版API会标记为过时，但不会删除；这意味着正式升级会相对容易些。
A major update is intended to introduce the latest popular development paradigms, approaches and massive improvements in architecture and functionality. Generally, it introduces incompatible changes in the core architecture, functional features, application-level API, underlying libraries and their versions. Major updates have no obligation to be backwards compatible; nevertheless, in new major versions, legacy API is not removed but goes deprecated, meaning that formal upgrade may be performed relatively easy.

# Unstable and Preview Versions
# 非稳定版本和预览版本
你可能还会需要用到开发版本：
You may also need to use development stage versions:

## SNAPSHOT VERSION
## 快照版本
被标记为
主要.次要-SNAPSHOT。
它是开发分支的每晚构建版本。此类版本可用来最先使用新功能特性。
is marked as
major.minor-SNAPSHOT.
It is a nightly build of the development branch. Such versions may be used for early access to the newest features.

## ALPHA VERSION
## 阿尔法版本
被标记为
主要.次要[.维护].ALPHAx。
作为即将发行版本的早期预览。
is marked as
major.minor[.maintenance].ALPHAx
and acts as an early preview of the upcoming release.

## BETA VERSION
## 测试版（BETA版）
被标记为
主要.次要[.维护].BETAx。
作为即将发行版本的最终且功能完备的预览。
is marked as
major.minor[.maintenance].BETAx
and acts as a final and feature-complete preview of the upcoming release.

# Support Terms
# 支持条款

## 5 YEARS
## 5年
免费维护
Free maintenance

每个最新的次要版本提供5年维护。这意味着主要版本的稳定次要版本发行后5年内会推出维护版本。主要版本的新次要版本发行后，前一个次要版本会有3个月的免费维护时间。因为假定这段时间足够您将应用程序升级到最新的次要版本。
is provided for 5 years for each latest minor version. This means maintenance releases will be issued within 5 years since the latest stable minor update is released for each major version of CUBA Platform. **Free maintenance** is also provided for 3 months for the previous minor version of each major release since the new minor version gets released. Such a period is assumed to be enough to update your application to the latest minor version.

例如，版本 100.8.x 于3000年9月1日发行。前一个次要版本 (100.7.x) 会免费维护到3000年11月30日。版本 100.8.x 则成为主要版本 (100) 的最新的次要版本，所以版本 (100.8.x) 将再获得5年的免费支持，直到3005年8月31日为止，或者直到新的次要版本 (100.9.x) 发行。
For example, version 100.8.x gets released on September, 1 3000. The penultimate minor version (100.7.x) will be under free maintenance until November, 30 3000. Version 100.8.x becomes the latest minor version for the major release 100. So, version 100.8.x will be under free maintenance support for another 5 years until August, 31 3005; or until the new minor version (100.9.x) gets released.

需要注意的是，尽管我们承诺遵守这项免费维护政策，但这不具有法律约束力，并且可能会发生变化。
Please note, that while we are committed to sticking to this free maintenance policy, it is not a legally binding offer and may possibly change.

## 10 YEARS
## 10年

Commercial maintenance
商业维护

适用于过去10年内发行的任何次要版本的CUBA平台。此项适用于更新周期比较长的项目。另外，如果最新的次要版本已超过5年，也可以使用此选项将维护时间再延长5年。
is available for any minor version of CUBA Platform, issued within 10 past years. This option could be useful for projects with a long update cycle. Also, staying on the latest minor version longer than 5 years, it is also possible to use this option to extend maintenance for another 5 years.

您可以在[此处](https://www.haulmont.com/services/cuba-platform-services/support/)了解更多有关商业维护的信息。
You can learn more about the commercial maintenance service [here](https://www.haulmont.com/services/cuba-platform-services/support/).

## AFTER
## 以后

尽头
End of life

发生在发行日期后10年（3650天）。换句话讲，即使是商业上也不维护10年以上的版本。
of a version happens after 10 years (3650 days) since the release date. In other words, versions older than 10 years are not maintained even on a commercial basis.