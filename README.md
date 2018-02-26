# README

This project implements an rpm spec file for creating a virtual
package consisting of a suite of Perl modules for interacting with
Amazon Web Services.

This is a wip.  Current plans are to:

* create a publicly available yum repository for the suite of of Perl
  modules that will be installed by this virtual rpm
* implement as much as possible of the Amazon API suite

# Motivation

While the `boto` Python library for AWS appears to be the most up to
date and maintained interface, languages other than Perl also seem to
have a robust enough community to support a standard and well
maintained API.  Perl has struggled to find a standard interface.
There are a host of individual interfaces on CPAN for various APIs,
however other than the PAWS interface, there has not to my knowledge
been a serious attempt to create a standardized Perl API for Amazon
Web Services.

# What's Wrong with PAWS?

Nothing really, if you want to embed a fairly large set of
dependencies that increase you startup time and fill up memory with
perhaps unneeded and unwanted components.

To be honest, the PAWS effort is probably much more likely to succeed
as a standard, is probably far better written, and has broader
community support. **I still believe however, that there is a need for a
lighter weight solution that allows developers to pick and chose their
components and is likely to use only the Perl modules that are found
in Amazon's repos.**

One of my objections to PAWS early on, was the difficulty in
installing it on an Amazon Linux AMI using the Redhat Package
manager. Although there is a strong case to be made for using `cpanm`
and *vendoring* as a way to separate the system Perl from your
application Perl to avoid the use of the Redhat Package Manager
altogether, there is still a lot of benefit to using the Redhat
Package Manager.  While the use of rpms is not wholly incompatible
with the notion of *vendoring*, it would be a fairly big effort to
create a separate yum repository divorced from the system Perl for the
various versions of Perl available.

# The Implementation

The idea behind this implementation is simple.  Create an API class
that is lightweight, yet allows easily sub-classing for each of the
AWS services.  In other words, all API services will inherit from a
lightweight Perl module (`Amazon::API`) and override its methods if
necessary (which we hope is seldom to never).

# The Problem

The organic nature of the Amazon API ecosystem has created a challenge
for anyone attempting to create a unified interface.  The RESTful
services employ a dizzying array of conventions for paramaters,
headers, protocols, service names and return responses.  The
documentation that is available from Amazon is sometimes lacking in
completeness and unfortunately sometimes just wrong.

# Status

So far, we have a working version of:

* `Amazon::API`
* `Amazon::Credentials`

...and example service APIs in various stages of implmentation. The
working model is to get the service API started in order to flush out
any issues or missed requirements for the `Amazon::API` core.

* `Amazon::SSM`
* `Amazon::EC2`
* `Amazon::CloudWatchEvents`
* `Amazon::IAM`

## Other Perl Modules on Deck

* `Amazon::KMS`
* `Amazon::DynamoDB`

## Other Perl Modules that Implement APIs

There are a few other Perl modules outside the framework of PAWS that
implement various services.  The plan is to use them as a model if
possible, but to rewrite them for use with the `Amazon::API` core
service.

* `Amazon::S3`
* `Amazon::SQS`
* `Amazon::SNS`

## Other AWS Related Perl Modules that May be Dependencies

* `AWS::Signature4`

# Building the Virtual Package

```
sudo yum install -y rpm-build
mkdir -p ~/rpmbuild/{BUILD,BUILDROOT,RPMS,SPECS,SOURCES,SRPMS}
echo -e "%_topdir ~/rpmbuild" > ~/.rpmmacros
git clone https://github.com/rlauer6/perl-aws-api.git
cd perl-aws-api
rpmbuild -bb perl-aws-api.spec
```
