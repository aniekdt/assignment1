---
title: Assignment 1
author:
  - Hendrik Werner s4549775
  - Aniek Den Teuling s1010747
date: \today
fontsize: 12pt
geometry: margin=5em
---

# 1
## a

* Require passsword to use `rm`

	**/etc/pam.d/rm**

		auth sufficient pam_unix.so

	Szenario: Peter always accidentally deleted his files. Therefore he wants to require athentication, so this does not happen as often.

* 2-factor authentication for `dd`

	**/etc/pam.d/dd**

		auth reqisite pam_second_factor.so
		auth sufficient pam_unix.so

	Szenario: It's very easy to do damage with `dd`, so the admin wants to make sure that users really are who they pretend to be.

* Require either password or second factor key to use `less`

	**/etc/pam.d/less**

		auth sufficient pam_second_factor.so
		auth required pam_unix.so

	(There is no module named `pam_second_factor.so`, but there is, for example, a `pam_google_authenticator.so` module, which is not default, but can be installed.)

	Szenario: `less` is pretty harmless, so the admin wants to make it's use easier, so he allows to use your password, or your athentication code. Users can use whatever is more convenient at the time.

* Write to some logfile after login

	**/etc/pam.d/login**

		auth required pam_unix.so
		auth optional pam_tally.so file=/var/log/login

	Szenario: A reseachers wants to find out how many people sucesfully log into the computer every day. He uses this ruleset to keep logs.

## b

i. Disable remote password logins for the specified users.

		auth required pam_unix.so

	First we simply ask all users to authenticate with their password.

		auth required pam_listfile.so item=user sense=deny file=/tmp/users

	This reads users from the given file, and denies authentication if they are found. So if users are on the list they cannot log in, even if their password was correct.

ii. Disable remote public key logins for specified users.

		auth required pam_ssh.so

	First we allow the user to autheticate using his public key.

		auth required pam_listfile.so item=user sense=deny file=/tmp/users

	Next we look up whether the user is allowed to log in. If his name is found on the list, authentication is denied.

iii.
	Bypass authentication and allow remote user logins without a valid password or authorized public key.

		auth sufficient pam_listfile.so item=user sense=allow file=/tmp/users

	This rule simply accepts any user that is on the list. I do not know why ssh still prompts for a password, because it accepts any password (except the empty password ""). Maybe this is due to the shh configuration.

# 2
