# Versioning and Support
CUBA Platform follows a standard semantic numeration for its stable versions - major.minor.maintenance, e.g. 7.2.4
Major version "7"
Minor version "2"
Maintenance version "4"

## MAINTENANCE RELEASE
A maintenance release is backwards compatible, bringing mostly bugfixes, minor improvements, fixes for critical performance and security issues. In rare cases, it may include some minor features or API enhancements, which do not introduce breaking changes.

## MINOR RELEASE
A minor release is mostly backwards compatible, however, it may include some breaking changes on the functional and API levels. All breaking changes are listed in the [release notes](https://www.cuba-platform.com/download/previous-platform/) as well as a way to address them. Often, the required modifications in the source code and application configurations are applied automatically by CUBA Studio after a version upgrade. A minor release is intended to introduce new features, meanwhile keeping upgrade procedure fast and easy.

## MAJOR UPDATE
A major update is intended to introduce the latest popular development paradigms, approaches and massive improvements in architecture and functionality. Generally, it introduces incompatible changes in the core architecture, functional features, application-level API, underlying libraries and their versions. Major updates have no obligation to be backwards compatible; nevertheless, in new major versions, legacy API is not removed but goes deprecated, meaning that formal upgrade may be performed relatively easy.

# Unstable and Preview Versions
You may also need to use development stage versions:

## SNAPSHOT VERSION
is marked as
major.minor-SNAPSHOT.
It is a nightly build of the development branch. Such versions may be used for early access to the newest features.

## ALPHA VERSION
is marked as
major.minor[.maintenance].ALPHAx
and acts as an early preview of the upcoming release.

## BETA VERSION
is marked as
major.minor[.maintenance].BETAx
and acts as a final and feature-complete preview of the upcoming release.

# Support Terms

## 5 YEARS
Free maintenance

is provided for 5 years for each latest minor version. This means maintenance releases will be issued within 5 years since the latest stable minor update is released for each major version of CUBA Platform. **Free maintenance** is also provided for 3 months for the previous minor version of each major release since the new minor version gets released. Such a period is assumed to be enough to update your application to the latest minor version.

For example, version 100.8.x gets released on September, 1 3000. The penultimate minor version (100.7.x) will be under free maintenance until November, 30 3000. Version 100.8.x becomes the latest minor version for the major release 100. So, version 100.8.x will be under free maintenance support for another 5 years until August, 31 3005; or until the new minor version (100.9.x) gets released.

Please note, that while we are committed to sticking to this free maintenance policy, it is not a legally binding offer and may possibly change.

## 10 YEARS

Commercial maintenance
is available for any minor version of CUBA Platform, issued within 10 past years. This option could be useful for projects with a long update cycle. Also, staying on the latest minor version longer than 5 years, it is also possible to use this option to extend maintenance for another 5 years.

You can learn more about the commercial maintenance service [here](https://www.haulmont.com/services/cuba-platform-services/support/).

## AFTER

End of life
of a version happens after 10 years (3650 days) since the release date. In other words, versions older than 10 years are not maintained even on a commercial basis.
