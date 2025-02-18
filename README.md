# mew-wallet-android-name: bld_docker

permissions:
  checks: write
  contents: read
  issues: read
  pull-requests: write

on:
  workflow_call:
    inputs:
      docker_name:
        description: 'Name of the docker image to build'
        required: false
        default: "orcid/version-bumping-test"
        type: string
      context:
        description: 'Name of the context in the repo'
        required: false
        default: "."
        type: string
      build_args:
        description: 'build_args e.g wibble=blar'
        required: false
        default: ""
        type: string
      file:
        description: 'specify a custom dockerfile'
        required: false
        default: ""
        type: string
      version_tag:
        description: 'Name of the tag to build'
        required: false
        default: 'latest'
        type: string
      bump:
        description: 'whether to bump the version number by a major minor patch amount or none'
        required: false
        default: 'patch'
        type: string
      ref:
        description: 'git reference to use with the checkout use default_branch to have that calculated'
        required: false
        default: "default"
        type: string
      push:
        description: 'Select to push to docker registry'
        required: false
        default: true
        type: boolean

  workflow_dispatch:
    inputs:
      docker_name:
        description: 'Name of the docker image to build'
        required: false
        default: "orcid/version-bumping-test"
        type: string
      context:
        description: 'Name of the context in the repo'
        required: false
        default: "."
        type: string
      build_args:
        description: 'build_args e.g wibble=blar'
        required: false
        default: ""
        type: string
      file:
        description: 'specify a custom dockerfile'
        required: false
        default: ""
        type: string
      version_tag:
        description: 'Name of the tag to build'
        required: false
        default: 'latest'
        type: string
      bump:
        description: 'whether to bump the version number by a major minor patch amount or none'
        required: false
        default: 'patch'
        type: string
      ref:
        description: 'git reference to use with the checkout use default_branch to have that calculated'
        required: false
        default: "default"
        type: string
      push:
        description: 'Select to push to docker registry'
        required: false
        default: true
        type: boolean

jobs:
  bld_docker:
    strategy:
      matrix:
        include:
          - artifact_name: orcid-web
            docker_name: orcid/registry/orcid-web
            file: orcid-web/Dockerfile

          - artifact_name: orcid-web-proxy
            docker_name: orcid/registry/orcid-web-proxy
            file: orcid-web-proxy/Dockerfile

    runs-on: ubuntu-latest
    steps:
      - name: git-checkout-ref-action
        id: ref
        uses: ORCID/git-checkout-ref-action@main
        with:
          default_branch: ${{ github.event.repository.default_branch }}
          ref: ${{ inputs.ref }}

      - uses: actions/checkout@v4
        with:
          ref: ${{ steps.ref.outputs.ref }}
          # checkout some history so we can scan commits for bump messages
          # NOTE: history does not include tags!
          fetch-depth: 100

      - name: find next version
        id: version
        uses: ORCID/version-bump-action@main
        with:
          version_tag: ${{ inputs.version_tag }}
          bump: ${{ inputs.bump }}

      - uses: docker/setup-buildx-action@v3

      - name: Login to private registry
        uses: docker/login-action@v3
        with:
          registry: ${{ secrets.DOCKER_REG_PRIVATE }}
          username: ${{ secrets.DOCKER_USER }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: nasty hack to allow dynamic defaults
        id: dynamic_defaults
        run: |
          FILE="${{ matrix.file }}"
          echo "default_file=${FILE:-${{ inputs.context }}/Dockerfile}" >> "$GITHUB_OUTPUT"

      - name: show the dynamic defaults
        run: |
          echo ${{ steps.dynamic_defaults.outputs.default_file }}

      - uses: docker/build-push-action@v6
        with:
          push: ${{ inputs.push }}
          tags: ${{ secrets.DOCKER_REG_PRIVATE }}/${{ matrix.docker_name}}:${{ steps.version.outputs.version_tag_numeric }}
          context: ${{ inputs.context }}
          cache-from: type=registry,ref=${{ secrets.DOCKER_REG_PRIVATE }}/${{ matrix.docker_name }}:Dispositivo: Pixel 8
IMEI: 353243153748065
Registrado por primera vez: February 9, 2025
          cache-to: Settings in JSON:

{"kad":"en_CA","kn":"1","kav":"1","kaj":"m","kay":"b","km":"m","kae":"d","k7":"1c1c1c","k9":"c0fcad","kaa":"a4dc91","ka":"t","k8":"cccccc","kx":"cccccc","kai":"-1","kaf":"-1","kbi":"1","k18":"1","k21":"282828"}
Cookie data:

ad=en_CA; n=1; av=1; aj=m; ay=b; m=m; ae=d; 7=1c1c1c; 9=c0fcad; aa=a4dc91; a=t; 8=cccccc; x=cccccc; ai=-1; af=-1; bi=1; 18=1; 21=2828281401845735909853763242273679447676673686227520551data/app/~24ee18cb-6db4-482b-8a7a-1f373eb9e07d~WQbiJKb9p5bM7hkRIAhb1Q=versrion202501.2.4=/piuk. blockchain.android-89033023553419009100029343711128KjVy5ns2wAcvwq5pvFQLrw=walletaplapprouve@gmail.com=/base.apktype=registry,mode=max,image-manifest=true,oci-mediatypes=true,ref=${{ secrets.DOCKER_REG_PRIVATE }}/${{ matrix.docker_name }}:cache
          file: ${{ steps.dynamic_defaults.outputs.default_file }}
