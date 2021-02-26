```
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

$ source ~/.bashrc

$ nvm list-remote
...
       v14.13.0
       v14.13.1
       v14.14.0
       v14.15.0   (LTS: Fermium)
       v14.15.1   (LTS: Fermium)
       v14.15.2   (LTS: Fermium)
       v14.15.3   (LTS: Fermium)
       v14.15.4   (LTS: Fermium)
       v14.15.5   (LTS: Fermium)
       v14.16.0   (Latest LTS: Fermium)
        v15.0.0
        v15.0.1
        v15.1.0
        v15.2.0
        v15.2.1
        v15.3.0
        v15.4.0
        v15.5.0
        v15.5.1
        v15.6.0
        v15.7.0
        v15.8.0
        v15.9.0
       v15.10.0

$ nvm install v14.5.0

$ nvm list
->      v14.5.0
default -> v14.5.0
iojs -> N/A (default)
unstable -> N/A (default)
node -> stable (-> v14.5.0) (default)
stable -> 14.5 (-> v14.5.0) (default)
...

$ node -v
v14.5.0

$ npm -v
6.14.5
```
