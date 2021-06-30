# Validator
[![Godoc](http://img.shields.io/badge/go-documentation-blue.svg?style=flat-square)](https://godoc.org/github.com/datewu/validator)

## Description
Trust, but verify
A validator package

## Usage
```golang
package main

import "github.com/datewu/validator"

// User ...
type User struct {
	Name      string    `json:"name"`
	Email     string    `json:"email"`
	Password  password  `json:"-"`
}

type password struct {
	plainText *string
	hash      []byte
}

func ValidateEmail(v *validator.Validator, email string) {
	v.Check(email != "", "email", "must be provided")
	v.Check(validator.Matches(email, validator.EmailRX), "email", "must be a valid email address")
}

func ValidatePasswordPlaintext(v *validator.Validator, password string) {
	v.Check(password != "", "password", "must be provided")
	v.Check(len(password) >= 8, "password", "must be at least 8 bytes long")
	v.Check(len(password) <= 72, "password", "must not be more than 72 bytes long")
}

func ValidateUser(v *validator.Validator, user *User) {
	v.Check(user.Name != "", "name", "must be provided")
	v.Check(len(user.Name) <= 500, "name", "must not be more than 500 bytes long")

	ValidateEmail(v, user.Email)

	if user.Password.plainText != nil {
		ValidatePasswordPlaintext(v, *user.Password.plainText)
	}

	if user.Password.hash == nil {
		panic("missing password hash for user")
	}
}

func main() {
	user := &User{Email: "ad@wutuofu.com"}
	v := validator.New()
	if ValidateUser(v, user); !v.Valid() {
	    fmt.Println("not cool")
		return
	}
	fmt.Println("cool")
}
```
