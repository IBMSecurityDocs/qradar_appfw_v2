# Removal of "V1+ (CentOS with Python 2)" option from Minimum Permitted App Base Image Stream

Starting with QRadar UP (UpdatePackage) 14 you will not be able to select V1+ (CentOS 6 with Python 2) as an application base image from `Admin -> System settings -> Minimum Permitted App Base Image Stream`.

## Content

( **V1+ (CentOS with Python 2)** ), which is based on CentOS 6 and Python 2, is now officially End of Support. Hence, it is essential to remove this option to align with IBM’s security policies and industry compliance standards. From QRadar UP (UpdatePackage) 14 onwards, existing CentOS 6/Python 2 based applications will no longer be supported. Existing CentOS-based applications running after patches in earlier versions will automatically move into an error state.

If your system is configured with V1 as the Minimum Permitted App Base Image, QRadar versions prior to UpdatePackage 14 automatically adjust it. The system sets it either to the minimum base image required for the applications to run or to V4, which is the default.

This ensures that no QRadar environment continues to run on deprecated CentOS-based app images, aligning with IBM’s security standards and customer compliance requirements.

## Migration Resources

- [Migrating from App Framework v1 to v2 (IBM Docs)](https://ibmsecuritydocs.github.io/qradar_appfw_v2/docs/tutorials/migrating_from_app_framework_v1_to_v2.html)
- [Migrate applications from app framework v2 or v3 to v4](https://www.ibm.com/support/pages/node/7160424)
- [New Features in App Framework v2](https://ibmsecuritydocs.github.io/qradar_appfw_v2/docs/documentation/new_features_appfw_v2.html)

## Support References

- [QRadar: Applications, CentOS 6, and Python 2 End Of Support](https://www.ibm.com/support/pages/node/6356547)
- [Security Bulletin: IBM QRadar SIEM Application Framework v1 (CentOS6) is End of Life](https://www.ibm.com/support/pages/node/6520674)
