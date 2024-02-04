# Signing with Notary

TODO: Intro

## Setup

* Make sure that Docker is up-and-running.
* Install `notation` CLI by following the instructions at https://notaryproject.dev/docs/user-guides/installation/cli/.


```bash
export IMAGE=cncf-demo-$(date +%Y%m%d%H%M%S)

notation cert generate-test --default "devopstoolkitseries.com"

cat signing/notary.json \
    | jq ".trustPolicies[0].registryScopes[0] = \"ttl.sh/$IMAGE\"" \
    | tee signing/notary.json.tmp

mv signing/notary.json.tmp signing/notary.json

notation policy import signing/notary.json
```

## Do

```bash
docker image build --tag ttl.sh/$IMAGE:v0.0.1 .

docker image push ttl.sh/$IMAGE:v0.0.1

export IMAGE_DIGEST=$(docker image inspect \
    --format='{{index .RepoDigests 0}}' ttl.sh/$IMAGE:v0.0.1)

notation ls $IMAGE_DIGEST

notation sign $IMAGE_DIGEST

notation verify $IMAGE_DIGEST
```

## Continue The Adventure

* [Access Control](../access/README.md)
