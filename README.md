TorchServe
=======

TorchServe (TS) is a flexible and easy to use tool for serving PyTorch models.

A quick overview and examples for both serving and packaging are provided below. Detailed documentation and examples are provided in the [docs folder](docs/README.md).

## Contents of this Document
* [Quick Start](#quick-start)
* [Serve a Model](#serve-a-model)
* [Other Features](#other-features)
* [Contributing](#contributing)


## Quick Start
### Prerequisites
Before proceeding further with this document, make sure you have the following prerequisites.
1. Ubuntu, CentOS, or macOS. Windows support is experimental. The following instructions will focus on Linux and macOS only.
1. Python     - TorchServe requires python to run the workers.
1. pip        - Pip is a python package management system.
1. Java 8     - TorchServe requires Java 8 to start. You have the following options for installing Java 8:

    For Ubuntu:
    ```bash
    sudo apt-get install openjdk-8-jre-headless
    ```

    For CentOS:
    ```bash
    sudo yum install java-1.8.0-openjdk
    ```

    For macOS:
    ```bash
    brew tap caskroom/versions
    brew update
    brew cask install java8
    ```

### Installing TorchServe with pip

#### Setup

**Step 1:** Setup a Virtual Environment

We recommend installing and running TorchServe in a virtual environment. It's a good practice to run and install all of the Python dependencies in virtual environments. This will provide isolation of the dependencies and ease dependency management.

One option is to use Virtualenv. This is used to create virtual Python environments. You may install and activate a virtualenv for Python 2.7 as follows:

```bash
pip install virtualenv
```

Then create a virtual environment:
```bash
# Assuming we want to run python2.7 in /usr/local/bin/python2.7
virtualenv -p /usr/local/bin/python2.7 /tmp/pyenv2
# Enter this virtual environment as follows
source /tmp/pyenv2/bin/activate
```

Refer to the [Virtualenv documentation](https://virtualenv.pypa.io/en/stable/) for further information.

**Step 2:** Install torch
TS won't install the PyTorch engine by default. If it isn't already installed in your virtual environment, you must install the PyTorch pip packages.

```bash
pip install torch torchvision
```

**Step 3:** Install TorchServe as follows:

```bash
git clone https://github.com/pytorch/serve.git
cd serve
python setup.py install
```

**Notes:**
* A minimal version of `model-archiver` will be installed with TS as dependency. See [model-archiver](model-archiver/README.md) for more options and details.
* See the [advanced installation](docs/install.md) page for more options and troubleshooting.

### Serve a Model

Once installed, you can get TS model server up and running very quickly. Try out `--help` to see all the CLI options available.

```bash
torchserve --help
```

For this quick start, we'll skip over most of the features, but be sure to take a look at the [full server docs](docs/server.md) when you're ready.

Here is an easy example for serving an object classification model:
```bash
torchserve --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar
```

With the command above executed, you have TS running on your host, listening for inference requests. **Please note, that if you specify model(s) during TS start - it will automatically scale backend workers to the number equal to available vCPUs (if you run on CPU instance) or to the number of available GPUs (if you run on GPU instance). In case of powerful hosts with a lot of compute resoures (vCPUs or GPUs) this start up and autoscaling process might take considerable time. If you would like to minimize TS start up time you can try to avoid registering and scaling up model during start up time and move that to a later point by using corresponding [Management API](docs/management_api.md#register-a-model) calls (this allows finer grain control to how much resources are allocated for any particular model).**

To test it out, you can open a new terminal window next to the one running TS. Then you can use `curl` to download one of these [cute pictures of a kitten](https://www.google.com/search?q=cute+kitten&tbm=isch&hl=en&cr=&safe=images) and curl's `-o` flag will name it `kitten.jpg` for you. Then you will `curl` a `POST` to the TS predict endpoint with the kitten's image.

![kitten](docs/images/kitten_small.jpg)

In the example below, we provide a shortcut for these steps.

```bash
curl -O https://s3.amazonaws.com/model-server/inputs/kitten.jpg
curl -X POST http://127.0.0.1:8080/predictions/squeezenet -T kitten.jpg
```

The predict endpoint will return a prediction response in JSON. It will look something like the following result:

```json
[
  {
    "probability": 0.8582232594490051,
    "class": "n02124075 Egyptian cat"
  },
  {
    "probability": 0.09159987419843674,
    "class": "n02123045 tabby, tabby cat"
  },
  {
    "probability": 0.0374876894056797,
    "class": "n02123159 tiger cat"
  },
  {
    "probability": 0.006165083032101393,
    "class": "n02128385 leopard, Panthera pardus"
  },
  {
    "probability": 0.0031716004014015198,
    "class": "n02127052 lynx, catamount"
  }
]
```

You will see this result in the response to your `curl` call to the predict endpoint, and in the server logs in the terminal window running TS. It's also being [logged locally with metrics](docs/metrics.md).

Now you've seen how easy it can be to serve a deep learning model with TS! [Would you like to know more?](docs/server.md)

### Stopping the running TorchServe
To stop the current running TorchServe instance, run the following command:
```bash
$ torchserve --stop
```
You would see output specifying that TorchServe has stopped.

### Create a Model Archive

TS enables you to package up all of your model artifacts into a single model archive. This makes it easy to share and deploy your models.
To package a model, check out [model archiver documentation](model-archiver/README.md)

## Recommended production deployments

* TS doesn't provide authentication. You have to have your own authentication proxy in front of TS.
* TS doesn't provide throttling, it's vulnerable to DDoS attack. It's recommended to running TS behind a firewall.
* TS only allows localhost access by default, see [Network configuration](docs/configuration.md#configure-ts-listening-port) for detail.
* SSL is not enabled by default, see [Enable SSL](docs/configuration.md#enable-ssl) for detail.
* TS use a config.properties file to configure TS's behavior, see [Manage TS](docs/configuration.md) page for detail of how to configure TS.

## Other Features

Browse over to the [Docs readme](docs/README.md) for the full index of documentation. This includes more examples, how to customize the API service, API endpoint details, and more.

## Contributing

We welcome all contributions!

To file a bug or request a feature, please file a GitHub issue. Pull requests are welcome.