# Security Remediation Plan

## Identified Security Vulnerabilities

The primary security vulnerabilities identified stem from two key issues:

- **Unauthorized Guest Access:** Improper configuration of access controls allowing guest users to log on to the SMB share "REPORTS."
- **Insufficient File Cleanup:** Failure of local administrators and users to properly clean up sensitive files, resulting in the retention of login credentials within Excel macros and configuration files for unattended installations.

These vulnerabilities pose a significant risk, as they could potentially expose critical authentication data to unauthorized individuals, increasing the likelihood of unauthorized access and exploitation of the network.

## Recommended Remediation Steps

To effectively remediate these issues, the following steps should be taken:

1. **System Scrubbing:** Conduct a thorough review and cleanup of the system to identify and remove any leftover configuration files associated with unattended installations. This ensures no sensitive data remains within the system.

2. **Disable Guest Access to SMB Shares:** Immediately disable guest access to the SMB share "REPORTS" to prevent unauthorized users from gaining access to sensitive information.

3. **Establish Strong Policies:** Implement and enforce strict policies for both users and administrators, explicitly prohibiting the storage of passwords in clear text locally. Educate staff on secure password management practices and implement technical controls to minimize the risk of sensitive data being exposed.

By addressing these points, the system will be better secured and the risk of unauthorized access or data breaches significantly reduced.

## Additional Information

For further details on how access was gained and to explore various methods for locking down guest access to SMB shares, please refer to the **Guides** folder. This folder contains comprehensive documentation on the access exploitation process and step-by-step instructions on securing SMB shares, ensuring that guest access is properly restricted and that sensitive data is protected.
