---
title: "Firebase Custom Builder for Google Container Builder"
date: 2018-06-22T7:24:56-07:00
draft: false
---

Google Container Builder  — or as my subconscious likes to call it
[google cloud builder][gcb-name] — has support for running arbitrary build steps. Something I needed but couldn't find was
a build step for publishing a site to Firebase's Hosting product.

I've created a custom build step for Firebase that you can use in your builds.

You can find the build step on GitHub at
[github.com/0xRLG/firebase-cloud-builder](https://github.com/0xRLG/firebase-cloud-builder)

Instructions for using the custom step can be found in the README.md but essentially can use
the build step just like any of the community build steps:

{{< highlight "shell" >}}
git clone https://github.com/0xRLG/firebase-cloud-builder
cd firebase-cloud-builder
cloud container builds submit --config cloudbuild.yaml .
gcloud container images list --filter firebase
{{< / highlight >}}

Once you've ran throught these steps  you should be able to use the image
"gcr.io/${PROJECT_ID}/firebase" as a build step in your own cloudbuild.yaml.

# Encrypting Firebase Keys

Part of the magic of a cloudbuild.yaml is that it contains *all* the information you need to
run a build in one convenient file you can commit to source control.

Most things in this file are great to share across the entire project — you wouldn't want each
member to build their own config from scratch — so it makes sense to commit this file to your
repository.

It's still a good idea to commit and publish build configurations that no individual is expected
to run on their own — such as deploying a function to production — because it another avenue for
knowledge sharing and collaboration between project members. Even it individuals don't run a build
on their own initiative it's useful to know what's going to happen so you can plan accordingly.

An issue arises when using the firebase builder to interact with a project in production — to
deploy a site for example — one needs to include *every bit if information needed* to deploy
including an authentication token.

Anyone with access to your firebase token can make the same changes that your build jobs can so it's
not a great idea to publish these tokens outside of where they're strictly needed. The principle of
least privilege applies here.

Luckily GCB has built in support for reading secrets encrypted using Google's
[Key Management Service][kms].


The GCB docs on the subject of encrypted key are very good but for the sake of complete & concrete
examples here's the section from my history with some annotations

---

First make sure gcloud and firebase are setup with the right projects. Second find the name of
your container builder by running the following command:

{{< highlight "shell" >}}
gcloud projects get-iam-policy <PROJECT_ID>
{{< / highlight >}}

The Service account will be in the form "{SERVICE_ACCOUNT}@cloudbuild.gserviceaccount.com"

Then you can run the following commands:

{{< highlight "shell" >}}
gcloud kms keyrings create build-keyrrin --location=global
gcloud kms keys create deploy  --location=global --keyring=build-keyring --purpose=encryption
gcloud kms keys add-iam-policy-binding deploy \
  --location=global --keyring=build-keyring \ 
  --member=serviceAccount:{SERVICE-ACCOUNT}@cloudbuild.gserviceaccount.com \
  --role=roles/cloudkms.cryptoKeyEncrypterDecrypter
firebase login:ci --no-localhost
# Try to not actually commit this one to bash history, start the line with a space
 echo -n "<SECRET>" | gcloud kms encrypt --plaintext-file=- \
   --ciphertext-file=- --location=global --keyring=build-keyring \
   --key=deploy | base64 -w0
{{< / highlight >}}

You then should be able to use the output of the last command to as the "SECRET_ENV" variable of
your cloudbuild.yaml:

{{< highlight "yaml" >}}
secrets:
  - kmsKeyName: projects/neat-vent-139904/locations/global/keyRings/build-keyring/cryptoKeys/deploy
    secretEnv:
      FIREBASE_TOKEN: <OUTPUT>
{{< / highlight >}}

Finally, set the firebase step to load the FIREBASE_TOKEN from the secret store by setting the
following on the build step

{{< highlight "yaml" >}}
  secretEnv:
   - FIREBASE_TOKEN
{{< / highlight >}}

## Complete cloudbuild.yaml

You can find a complete example in the [cloudbuild.yaml][gh-cloudbuild] for this site on GitHub
[github.com/rlguarino/rlg.io][gh-rlg]

The important bits are:

{{< highlight "yaml" >}}
steps:
# [...]
- name: gcr.io/neat-vent-139904/firebase
  args:
   - deploy
   - --project
   - site-ce15d
  secretEnv:
   - FIREBASE_TOKEN
secrets:
  - kmsKeyName: projects/neat-vent-139904/locations/global/keyRings/build-keyring/cryptoKeys/deploy
    secretEnv:
      FIREBASE_TOKEN: CiQAPi2Yo6ApJO4Ez0blG31ZUEk19xBdQEyICO3A1OCjpVvRbkASVgACg+475/JwBU5mjdpXY7X2crEooIQg/WIt2W87THf4StuyrbmKDihL7qpNedalIwwwCXTiqtIntHTGYj3YCZcMdigtvroTkvhZVFmqvT5Ljz5zkgjc

{{< / highlight >}}

[gcb-name]: {{< ref "blog/2018/06/container-builders-name/index.md" >}}
[gh-cloudbuild]: https://github.com/rlguarino/rlg.io/blob/master/cloudbuild.yaml
[gh-rlg]: https://github.com/rlguarino/rlg.io
[kms]: https://cloud.google.com/kms/
