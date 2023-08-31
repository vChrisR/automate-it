---
title: 'BOSH Release blobs'
date: Tue, 06 Feb 2018 11:00:53 +0000
draft: false
tags: ['blobs', 'BOSH', 'BOSH', 'Cloud Foundry', 'cloudfoundry', 'ITQ', 'S3']
---

In my attempt to get Cloudfoundry running on Raspberry PIs I had to make some changes to a few BOSH releases. Most of the work involved swapping out blobs with other blobs. At first it wasn't very clear to me how the blob store thingy in the BOSH releases work so I thought I'd be good to share what I learned.

Anatomy of a BOSH Release
-------------------------

A BOSH release consists of two main parts: Jobs and Packages. Jobs consist of a definition of how to start the software (monit and control files) and how to configure it (template files). Packages contain a script which takes care of compilation and installation of the software , a list of files needed for the package and a list of dependencies it might have on other packages. The actual software or source code can be added to the bosh release in two ways: you can put it in the src folder (copy it or use submodules) in the release or add it as a blob. Files referenced in your BOSH release will be looked up in src first, then in blobs.

Where are the blobs anyways?
----------------------------

If you git clone the source of a BOSH release from github you'll probably notice that you won't get the blobs that are included in the release. So where did the blobs go? turns out that when you create a final release of you BOSH release the BOSH CLI will upload the blobs you added to your release to a publicly readable S3 bucket. When you then download the release source and create a dev release (bosh create-release --force) the BOSH CLI will lookup the bucket to use in config/final.yml and the list of blobs in config/blobs.yml and download the blobs to the blobs folder in the release dir.

How to add my own blobs?
------------------------

Adding blobs to a release is easy: bosh add-blob <path to file> <relative path in blobstore> BOSH will copy the file to the blobs directory using the name you give as the last parameter. It will also create the entry in blobs.yml for you. But this blog post is mostly written out of the perspective of working on a fork of an existing release. So imagine you create a fork of an existing BOSH release github project. You want to swap out some of the blobs that are already in the release and create a new release out of that. If you'd just add the blobs en then run bosh create-release --final or bosh upload-blobs  you'll get an error because BOSH won't be able to upload to the public (read-only) S3 bucket where the current blobs reside. You'll need to copy all the blobs over to a bucket you'll have control over. Here is how you do that:

*   fork and git clone the release repo
*   add your blobs using bosh add-blob
*   delete blobs that you no longer need using bosh delete-blob  or just remove the reference from blobs.yml
*   run: bosh create-release --force  this will force bosh to download all blobs to you local disk
*   Configure S3 (you'll need an Amazon AWS account):
    *   Create an S3 bucket and make it readonly for the world
    *   Create a policy, group and user to assign RW permissions
    *   Generate API key for this user
*   change config/final.yml to point to your bucket
*   create config/private.yml:

```
\---
blobstore:
options:
access\_key\_id: <S3 access id key>
secret\_access\_key: <S3 secret>

```run bosh upload-blobs  or bosh create-release --final --tarball <path to output tgz> That's it. You now have your completely forked bosh release with you own blob store.