## **W: GPG error: https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/ InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 51716619E084DAB9**

---

```
// NO_PUBKEY 뒤에 나오는 공개키를 아래 명령어를 통해 등록해준다.
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys [공개키]
```
