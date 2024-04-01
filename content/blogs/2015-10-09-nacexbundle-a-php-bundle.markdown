---
title: 'NacexBundle, a PHP bundle'
date: 2015-10-09 08:53:39 +0000
tags: [symfony,php,nacex]
---
Today I am releasing one of the php bundles we made for Selltag, the [NacexBundle][bundle].

NacexBundle is a bundle implementing a client/service to call the [Nacex SOAP API][nacex_soap], [the transport company Nacex][nacex] API.

We use it with *Symfony*, but you could use It as you please. :-)

[You can read the instructions for Installing and configuring It][readme], basically It would be something like:

Install through composer.

<pre><code class="language-bash">composer require selltag/nacex-bundle
</code></pre>


Configure the service parameters.

<pre><code class="language-bash">selltag_nacex:
    nacex_password: %nacex_username%
    nacex_password: %nacex_password%
    nacex_url: %nacex_url%
</code></pre>

You can define `nacex_username`, `nacex_password` and `nacex_url` in your `parameters.yml`.

In symfony you could use It as a service from the container.

<pre><code class="language-php">$nacexClient = $this->getContainer()
    ->get('selltag_nacex.nacex_client');

$data = array(
    'reco_codigo' => null,
    'Del_Sol'     => '0001',
    'Num_Rec'     => '123456',
    'ref'         => null
);

$result = $nacexClient->putRecogida($data);

$recogida = array(
    'code'        => $result[0],
    'date'        => $result[1],
    'time'        => $result[2],
    'observation' => $result[3],
    'status'      => $result[4],
    'status_code' => $result[5]
);
</code></pre>

If you don't want to use It as a service, you can just:

<pre><code class="language-bash">use Selltag\NacexBundle\Services\NacexClientService;

$nacexClient = new NacexClientService(
    $nacexUser,
    $nacexPassword,
    $nacexUrl
);
</code></pre>


[Questions?][issue]

[issue]: https://github.com/javaguirre/NacexBundle/issues
[bundle]: https://github.com/javaguirre/NacexBundle
[readme]: https://github.com/javaguirre/NacexBundle/blob/master/README.md
[nacex]: https://www.nacex.es
[nacex_soap]: https://www.nacex.es/irIntegracionesWebservice.do

