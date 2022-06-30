User-reported CWL conformance testing results for Toil in various configurations.

Procedure:


In the script below, add any necessary toil-cwl-runner options to `extra_opts`,
add environment variable exports via `export`, and customize the
`variant` name to be unique.

``` sh
version=5.6.0
variant=singlemachine
extra_opts=""
dest=$PWD/toil_${version}_${variant}
mkdir --parents ${dest}
mkdir --parents ${dest}/cwl-v1.0
mkdir --parents ${dest}/cwl-v1.1
mkdir --parents ${dest}/cwl-v1.2

python3 -m venv toil-${version}
source toil-${version}/bin/activate
pip install -U pip setuptools wheel cwltest
pip install toil[cwl]==${version}

cat > ${dest}/README.md<<EOF
date: $(date --utc)
$(uname -a)
$(python -V)
toil-cwl-runner options: ${extra_opts}

docker version:
$(docker version)

podman version:
$(podman version)

singularity version:
$(singularity version)
EOF

git clone https://github.com/common-workflow-language/common-workflow-language.git cwl-v1.0
pushd cwl-v1.0 ; \
  ./run_test.sh --junit-xml=${dest}/cwl-v1.0/junit.xml --badgedir=${dest}/badges RUNNER=toil-cwl-runner EXTRA="${extra_opts}" ; \
  popd

git clone https://github.com/common-workflow-language/cwl-v1.1.git
pushd cwl-v1.1 ; \
  ./run_test.sh --junit-xml=${dest}/cwl-v1.1/junit.xml --badgedir=${dest}/badges RUNNER=toil-cwl-runner EXTRA="${extra_opts}" ; \
  popd

git clone https://github.com/common-workflow-language/cwl-v1.2.git --branch main
# this repo defaults to the CWL v1.2.1 development branch
pushd cwl-v1.2 ; \
  ./run_test.sh --junit-xml=${dest}/cwl-v1.2/junit.xml --badgedir=${dest}/badges RUNNER=toil-cwl-runner EXTRA="${extra_opts}" ; \
  popd
```

Then customize the generated README.md to include other relevant configuration
information and delete unneed information (like version information of container
engines that were not used). The please send a pull request to this repository
to share your results.

Thanks!
