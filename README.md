# Production Ready: pesde registry

Easily start up a [pesde](https://pesde.dev/) registry with this docker stack.

> [!WARNING]
> This guide is not conclusive. Please read the respective wikis on setting up the various tools used in this setup if you have any issues. I will not help with server setup, configuration, etc.

## Installation

1. Getting a server - [Free Oracle Cloud](https://github.com/hitrov/oci-arm-host-capacity)

    You can start for free via Oracle Cloud's free tier, giving you a free 24/7 ARM 4 OCPU, 24 GB RAM VM with 50 GB of storage space. This should be enough for most of your needs. However, getting a server can be difficult since there's limited spaces. To get around that, you can use a script to automatically try to get a server each minute. You can view it [here](https://github.com/hitrov/oci-arm-host-capacity).

2. Domain registration - [Namecheap](https://www.namecheap.com/)

    You need a domain name for this, any will work. From my experience, `xyz` TLDs are the cheapest at around $1 for the first year.

3. Protection - [Cloudflare](https://www.cloudflare.com/en-gb/)

    I strongly recommend you use Cloudflare to protect your servers from DDoS and attacks. View guides online for setting this up for your specific registrar.

4. Protection - [Hardening access](https://github.com/kingcc/cloudflare-ips-only/blob/master/host.sh)

    A simple way to protect your server is to only allow Cloudflare IPs to access your server. The [linked script](https://github.com/kingcc/cloudflare-ips-only/blob/master/host.sh) automatically adds the needed `iptables` rules for you. However, **you need to save it manually**. These rules should make it so the ports for HTTP and HTTPS can only be accessed via cloudflare. Other ports will remain open like normal.

5. Protection - [Other](https://github.com/dreamsofcode-io/zenstats/blob/main/docs/vps-setup.md)

    You can view more recommended things in the [linked guide](https://github.com/dreamsofcode-io/zenstats/blob/main/docs/vps-setup.md) which covers making a new user and hardening SSH. I recommend you make a user for `traefik` and another user for the pesde `registry` itself.

6. CD (Optional) - [Video 1](https://www.youtube.com/watch?v=fuZoxuBiL9o), [Video 2](https://www.youtube.com/watch?v=F-9KWQByeU0)

    [Dreams of Code](https://www.youtube.com/@dreamsofcode) has some great videos showing you further setup on the CD and things like `docker context` and `docker stack`. The CD will let you automatically make changes to the configuration on GitHub and see them deployed onto the server. I have already put the workflow [here](./.github/workflows/deploy.yaml). If you want to use it, follow [Video 1](https://www.youtube.com/watch?v=fuZoxuBiL9o) by creating the `DEPLOY_SSH_PRIVATE_KEY` secret and setting the `DEPLOY_HOST` inside of the [workflow file](./.github/workflows/deploy.yaml).

## Configuration

Each folder ([pesde](./pesde/) and [traefik](./traefik/)) have their respective `.env.template` files. Simply make a copy of them, and edit the configuration to your liking.

> [!NOTE]
> You shouldn't need to edit any other files but the environment files. Edit other files with caution.

### Creating environment files from template

1. `cd` into each directory

    ```bash
    cd pesde
    ```

    ```bash
    cd traefik
    ```

2. Make a copy of the templates

    ```bash
    cp .env.template .env
    ```

3. Edit the template environment file to your liking

    ```bash
    nano .env
    ```

    You can use `CTRL + X` to save in `nano`.

### Editing the environment files

In each template file, I've put the minimal recommended variables. I have also linked to the respective guides on additional variables you can put.

For the [pesde configuration](./pesde/.env.template), I strongly recommend making a new GitHub user and the password must be a [PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens). Make sure the user has at least `push` permissions to the `index` repository.

For the [traefik configuration](./traefik/.env.template), you're asked for some Google API details. This makes your `traefik` instance more secure by adding a strong OAuth allowlist on accessing critical parts like the dashboard and `whoami`. You can view the official guide on setup [here](https://github.com/thomseddon/traefik-forward-auth/wiki/Provider-Setup#google).

## Making the index repository

The index is a repository that contains metadata about all the packages in the registry. You can view the official guide on making it [here](https://docs.pesde.dev/guides/self-hosting-registries/#making-the-index-repository).

## Running

Finally, you can run the entire stack with these commands, assuming you're `cd` into the respective directory on the server

```bash
docker stack deploy -c docker-stack.yaml traefik
```

```bash
docker stack deploy -c docker-stack.yaml pesde
```

Alternatively, you can also deploy via GitHub actions as mentioned in step 6 of [installation](#installation).
