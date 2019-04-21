go get -u github.com/rogpeppe/godef
go get -u github.com/golang/lint/golint
go get -u github.com/derekparker/delve/cmd/dlv
go get -u github.com/tpng/gopkgs

install Go debugger in vscode
press F5 or check for gear to conigure the launch.json
https://github.com/Microsoft/vscode-go/wiki/Debugging-Go-code-using-VS-Code



// go test -cover -v ./socialLogin
// go test -cover -v ./...

// https://golangbot.com/structs-instead-of-classes/

// build tests for react app
// actions
// reducer
// component

// complete facebook login route

// start building local registration
// start building local login

// setup callbacks on custom package
//		custom package for for all social logins

// setup package for postgres DB
// 		save email, password, and or access token?
//		- track ip and browser info?

// onload test if user is logged in?

// add customization to .env for
//		dev and production
//		specific login apis

// learn to code split apps
// 		this means load this partial into a greater app








// sessions
// option a
// https://github.com/wader/gormstore
// https://www.godoc.org/github.com/jinzhu/gorm#Open

// option b
// http://www.gorillatoolkit.org/pkg/sessions
// https://github.com/gorilla/sessions
// https://github.com/antonlindstrom/pgstore

// option c
// https://astaxie.gitbooks.io/build-web-application-with-golang/en/06.1.html
//     - postgres, sockets, json,
// https://github.com/astaxie/build-web-application-with-golang/blob/master/en/06.2.md

// sesison store
// https://github.com/antonlindstrom/pgstore
// encrypt
// https://astaxie.gitbooks.io/build-web-application-with-golang/en/09.6.html

// password hashing:
// https://gowebexamples.com/password-hashing/
//       - webs sockets, json, middleware


add jwt token to browser for form
    - add token class / interface
    - encrypt token
add GBToken for jwt token for form
    - GBtoken can be the timestamp first created to enript jwt token
submit form with token



Vendoring Our Dependencies
To vendor our project's dependencies, I highly recommend the govendor tool (https://github.com/kardianos/govendor).

$ go get -u github.com/kardianos/govendor
# Make sure you're in the correct project directory
$ govendor init
$ govendor add +external
