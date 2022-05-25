* https://docs.amplify.aws/cli/auth/groups/

# User groups

Amplify CLI 를 이용해 Cognito User Pool 에서 논리적인 group 을 생성하고 group 에 따라 접근 권한을 설정할 수 있다. "Admin" 이나 "Editors" 같은 단어로 그룹명을 정하고 상대적인 우선순위를 정할수 있다. 이런 그룹에 따라 AppSync, API Gateway, S3 bucket 등의 모든 resource 들의 접근 권한을 설정하는데 편리하다. 우선순위 설정은 모호함 없이 권한을 설정하는데 편리하기 때문이다.

## Create user groups
```bash
amplify add auth
```
```bash
❯ Manual configuration

Do you want to add User Pool Groups? (Use arrow keys)
❯ Yes

? Provide a name for your user pool group: Admins
? Do you want to add another User Pool Group Yes
? Provide a name for your user pool group: Editors
? Do you want to add another User Pool Group No
? Sort the user pool groups in order of preference …  (Use <shift>+<right/left> to change the order)
  Admins
  Editors
```

위의 예에서 물어본것처럼 `Shift` 키를 누른상태에서 **LEFT**, **RIGHT** 버튼을 눌러 그룹의 우선순위를 조정한다. 완료한 뒤에서는 `amplify/backend/auth/userPoolGroups/user-pool-group-precedence.json` 파일에서 수동으로 변경할수 있다.

## Group access controls

인증 사용자와 Guest 사이에 다른 access 제어 설정을 이용해 Amplfiy resource 들에 대한 CRUD 권한을 제어할수 있다.
또한 특정 User Pool Group 으로 로그인 된 사용자에 따라 다른 권한을 설정할 수 있다.

```bash
amplify add storage  # Select content
```

```bash
? Restrict access by? (Use arrow keys)
  Auth/Guest Users
  Individual Groups
❯ Both
  Learn more

Who should have access?
❯ Auth and guest users

What kind of access do you want for Authenticated users?
❯ create/update, read

What kind of access do you want for Guest users?
❯ read

Select groups:
❯ Admins

What kind of access do you want for Admins users?
❯ create/update, read, delete
```

위의 예는 권한 조함을 사용하고 있다. Admins 그룹에 속한 사용자에게는 모든 접근 권한을 주고 있고 Guest 사용자에게는 읽기 권한만 부여한다. 또한 어느 그룹에도 속하지 않은 인증 사용자에게도 CRUD 권한을 부여한다.
Amplify 는 덧붙여 IAM 정책에 따라 설정할 것이다.
사용자 권한은 `customPolicies` 를 `amplify/backend/auth/userPoolGroups/user-pool-group-precedence.json` 에 더하는 방식으로 더 자세하게 설정될 수 있다.
이것은 IAM 정책에 맞게 배포되는 동안 inline 정책으로 적용된다.
고급 설정은 AWS resource 에 대한 이해를 가지고 있을때만 적합하다는 것을 알고 있어야 한다.
예를 들어 Admins 그룹이 S3 bucket 을 생성할 수 있는 권한이 필요하다 같은 내용이 있어야 한다.

```JSON
[
  {
    "groupName": "Admins",
    "precedence": 1,
    "customPolicies": [
      {
        "PolicyName": "admin-group-policy",
        "PolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Sid": "statement1",
              "Effect": "Allow",
              "Action": ["s3:CreateBucket"],
              "Resource": ["arn:aws:s3:::*"]
            }
          ]
        }
      }
    ]
  },
  {
    "groupName": "Editors",
    "precedence": 2
  }
]
```