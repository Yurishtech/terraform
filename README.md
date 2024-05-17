# Page

Aliasing Providers

\


To define multiple configurations for the same provider, create a provider

alias. This is useful when you want to use the same provider in slightly

different ways, such as supporting multiple regions for a cloud platform.

\


\


provider "aws" {

&#x20; region  = "us-east-1"

}

\


provider "aws" {

&#x20; alias  = "secondary"

&#x20; region = "us-west-2"

}

\


\


Resources that do not specify a provider will use the default, or unaliased,

provider.

\


\


resource "aws\_instance" "app" {

&#x20; ami           = "ami-007855ac798b5175e"

&#x20; instance\_type = "t3.micro"

}

\


\


Set the provider attribute to use an aliased provider within a resource.

\


\


resource "aws\_instance" "app" {

&#x20; provider      = aws.secondary

&#x20; ami           = "ami-007855ac798b5175e"

&#x20; instance\_type = "t3.micro"

}

\


\
