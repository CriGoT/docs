---
section: appliance
description: PSaaS Appliance infrastructure information about DNS
topics:
    - appliance
    - infrastructure
    - dns
contentType: reference
useCase: appliance
applianceId: appliance31
sitemap: false
---

<!-- markdownlint-disable MD033 -->

# DNS

The following document details the requirements of DNS records used for PSaaS Appliance instances.

::: note
DNS records must be finalized for all of the tenants prior to PSaaS Appliance deployment. They cannot be changed afterwards.
:::

You’ll need one certificate per environment (such as if you have a Dev/Test environment and a Prod environment, you’ll need two certs).

If you’d like to use a [Webtask Dedicated Domain](/appliance/webtasks/dedicated-domains), you’ll need an additional DNS zone and certificate for each environment. If you have a Dev/Test environment and a Prod environment, you’ll need a two total of two certificates per environment.

If you’d like to use a [Custom Domains](/custom-domains), you’ll need an additional DNS zone and certificate for each environment. If you have a Dev/Test environment and a Prod environment, you’ll need a two total of three certificates per environment.

Dedicated and non-dedicated host names must be unique.

## Sample DNS Naming Scheme

<table class="table">
  <tbody>
    <tr>
        <th>Management Dashboard</th>
        <td>manage.yourdomain.com</td>
    </tr>
    <tr>
        <th>Configuration</th>
        <td>config.yourdomain.com</td>
    </tr>
    <tr>
        <th>Webtask</th>
        <td>webtask.yourdomain.com</td>
    </tr>
    <tr>
        <th>App Tenant(s)</th>
        <td>identity.yourdomain.com (for example); <br /> app-project.yourdomain.com (if you want more than 1 App tenant) <br />...and so on</td>
    </tr>
  </tbody>
</table>

For a dev/test non-production PSaaS Appliance a common practice is to include "dev” in the domain name:

<table class="table">
  <tbody>
    <tr>
        <th>Management Dashboard (Dev)</th>
        <td>manage.dev.yourdomain.com</td>
    </tr>
    <tr>
        <th>Configuration (Dev)</th>
        <td>config.dev.yourdomain.com</td>
    </tr>
    <tr>
        <th>Webtask (Dev)</th>
        <td>webtask.dev.yourdomain.com</td>
    </tr>
    <tr>
        <th>App Tenant(s) (Dev)</th>
        <td>identity.dev.yourdomain.com (for example); <br /> app-name.dev.yourdomain.com (if you want more than 1 App tenant)<br />...and so on</td>
    </tr>
  </tbody>
</table>

### Definitions of Terms Used in the DNS Naming Scheme

* **Configuration**: highly-privileged tenant used to do the PSaaS Appliance baseline configuration and for managing the security of other tenants;
* **App**: the name of your application;
* **yourdomain.com**: your organization's domain name.

![](/media/articles/appliance/infrastructure/appliance-dns.png)

## Multi-Tenancy

The Auth0 PSaaS Appliance is capable of supporting multi-tenancy (that is, each tenant may have one or more associated apps). Auth0 may recommend this deployment model when multiple groups within your company share the PSaaS Appliance for different projects. If a customer decides to create multiple app tenants, each app tenant must have its own DNS entry.

## DNS Configuration Requirements

In a standard multi-node cluster deployment, the DNS records will point to the IP address of the [load balancer in front of the cluster](/appliance/infrastructure/infrastructure-overview).

### IP Addresses and DNS Records

For a single-node PSaaS Appliance instance, the DNS record(s) will point to the IP address of the virtual machine itself (this is often the case for the development/test node).

::: note
  Auth0 does not recommend using the same wildcard certificate(s) for Production **and** non-Production (Test/Development) environments **or** mapping the DNS for both environments to the same servers.
:::

### Hostnames

The hostname (such as **manage-project**.yourdomain.com) must be at least three characters long and must **not** contain any underscores(_).

The following are reserved tenant names and **may not** be used for the **app** tenant.

<table class="table">
  <tbody>
    <tr>
        <td>login</td>
        <td>admin</td>
        <td>app</td>
        <td>manage</td>
        <td>blog</td>
    </tr>
    <tr>
        <td>ftp</td>
        <td>mail</td>
        <td>pop</td>
        <td>pop3</td>
        <td>imap</td>
    </tr>
    <tr>
        <td>smtp</td>
        <td>stage</td>
        <td>stats</td>
        <td>status</td>
        <td>dev</td>
    </tr>
    <tr>
        <td>logs</td>
        <td>www</td>
        <td>docs</td>
        <td>sdk</td>
        <td>ci</td>
    </tr>
    <tr>
        <td>docker</td>
        <td>styleguide</td>
        <td>ask</td>
        <td>it</td>
        <td>cdn</td>
    </tr>
    <tr>
        <td>api</td>
        <td>releases</td>
        <td>release</td>
        <td>spf</td>
        <td>feedback</td>
    </tr>
    <tr>
        <td>help</td>
        <td>support</td>
        <td>int</td>
        <td>auth</td>
        <td></td>
    </tr>
  </tbody>
</table>

::: warning
Please note that the [Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance) from **auth0** to the supplied name *must be greater than two*. This means that tenant names like **auth** or **authy** (and other similar names) cannot be used.

To see if your tenant name meets this requirement, you can validate your selections using a [Levenshtein Distance calculator](http://www.unit-conversion.info/texttools/levenshtein-distance/).
:::

The Management Dashboard, Configuration Tenant, and App Tenant(s) must all be a part of the same parent domain (such as yourdomain.com).

Three- or four-part domain names are supported (such as manage.project.yourdomain.com).

## Custom Domains

In the PSaaS Appliance, you may map any arbitrary domain name to a tenant using the Custom Domains feature.

Suppose these were your standard domains:

<table class="table">
  <tbody>
    <tr>
        <td>Sample Tenant</td>
        <td>Custom Domain for the Sample Tenant</td>
    </tr>
    <tr>
        <td>auth.example.com</td>
        <td>new-name.not-example.com</td>
    </tr>
  </tbody>
</table>

### Definitions of Terms Used in the DNS Naming Scheme

::: note
This functionality is available in appliance release [1906](https://auth0.com/releases/1906). If you are running an older version reach to you contact to update your environment.
:::

* **auth**: the name of your tenant.
* **example.com**: your organization's domain name.
* **cname.example.com**: This is the Cname hostname. This is a DNS hostname that will be used to verify custom domains when using Auth0 managed certificates.
* **edge.example.com**: The edge domain is a DNS zone used to create the endpoints that will be used to give access to custom domain request. This can be a sub zone of the main domain name or a completely different one.

### DNS Configuration requirements

In a standard multi-node cluster deployment

- The Cname hostname ( _cname.example.com_ ) will point to the IP address of the [load balancer in front of the cluster](/appliance/infrastructure/infrastructure-overview).
- The complete edge domain ( _*.edge.example.com_ ) should be a CNAME DNS record with the target set to Cname hostname ( _cname.example.com_ )

For a single-node PSaaS Appliance instance, the DNS record(s) will point to the IP address of the virtual machine itself (this is often the case for the development/test node).

### Edge domain names

#### Auth0 managed certificates

During the creation of a Custom Domain with Auth0 managed certificates Auth0 will create use a unique hostname in the edge domain, for example: _cd_123456789.edge.example.com_. This hostname will be used to:

- Verify the custom domain ownership by being the target of the CNAME DNS record you create for your custom domain ( _new-name.not-example.com_ ).
- Serve as the permanent link between your custom domain and the Auth0 infrastructure.

::: note
THe DNS verification peformed by Auth0 checks that the custom domain is a CNAME record that targets an edge domain host, and that the edge domaint host is a CNAME record that targest the Cname hostname.
:::

#### Self-managed certificates

During the creation of a Custom Domain with certificatesi managed by you Auth0 will create use a unique hostname in the edge domain, for example: _cd_987654321.edge.example.com_. This hostname will be used to:

- Serve the Auth0 content to your reverse proxy, as long as the right API key is provided.

