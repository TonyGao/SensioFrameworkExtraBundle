SensioFrameworkExtraBundle
==========================

The default Symfony ``FrameworkBundle`` implements a basic but robust and
flexible MVC framework. `SensioFrameworkExtraBundle`_ extends it to add sweet
conventions and annotations. It allows for more concise controllers.

默认Symfony的``FrameworkBundle``完成了一个基础而稳固的灵活MVC框架。
`SensioFrameworkExtraBundle`_ 将它扩展，添加了漂亮的协议和annotation。
它能够实现更加简明的控制器。

安装
---

Before using this bundle in your project, add it to your ``composer.json`` file:

在你的项目里使用这个bundle之前，将它添加到你的``composer.json``文件：

.. code-block:: bash

    $ composer require sensio/framework-extra-bundle

然后，就像其他bundle一样，将它包含进你的Kernel类里::

    public function registerBundles()
    {
        $bundles = array(
            // ...

            new Sensio\Bundle\FrameworkExtraBundle\SensioFrameworkExtraBundle(),
        );

        // ...
    }

.. _release-cycle-note:

.. 注意::

    Since SensioFrameworkExtraBundle 3.0 its release cycle is out of sync
    with Symfony's release cycle. This means that you can simply require
    ``sensio/framework-extra-bundle: ~3.0`` in your ``composer.json`` file
    and Composer will automatically pick the latest bundle version for you.
    You have to use Symfony 2.3 or later for this workflow. Before Symfony
    2.3, the required version of the SensioFrameworkExtraBundle should be
    the same as your Symfony version.

    自从 SensioFrameworkExtraBundle 3.0 它的发布周期就不再与Symfony的发布周期
    同步了。也就是说你可以简单的在你的``composer.json``文件里写入需要
    ``sensio/framework-extra-bundle: ~3.0``，Composer会为你自动挑选最新的bundle
    版本。要这个工作顺序正常，你需要使用 Symfony 2.3 或更新的版本。Symfony 2.3之前的
    版本，需求的 SensioFrameworkExtraBundle 应该与你的 Symfony 版本相同。

如果你计划为控制器使用或创建 annotation，确保你通过下边的一行更新了你的``autoload.php``::

    Doctrine\Common\Annotations\AnnotationRegistry::registerLoader(array($loader, 'loadClass'));

配置
---

All features provided by the bundle are enabled by default when the bundle is
registered in your Kernel class.

The default configuration is as follow:

只要此bundle登记到了你的 Kernel 类里，提供的所有特性默认都是开启的。

默认配置如下：

.. configuration-block::

    .. code-block:: yaml

        sensio_framework_extra:
            router:      { annotations: true }
            request:     { converters: true, auto_convert: true }
            view:        { annotations: true }
            cache:       { annotations: true }
            security:    { annotations: true }
            psr_message: { enabled: false } # 如果安装了PSR-7 bridge默认会是true


    .. code-block:: xml

        <!-- xmlns:sensio-framework-extra="http://symfony.com/schema/dic/symfony_extra" -->
        <sensio-framework-extra:config>
            <router annotations="true" />
            <request converters="true" auto_convert="true" />
            <view annotations="true" />
            <cache annotations="true" />
            <security annotations="true" />
            <psr-message enabled="false" /> <!-- Defaults to true if the PSR-7 bridge is installed -->
        </sensio-framework-extra:config>

    .. code-block:: php

        // load the profiler
        $container->loadFromExtension('sensio_framework_extra', array(
            'router'      => array('annotations' => true),
            'request'     => array('converters' => true, 'auto_convert' => true),
            'view'        => array('annotations' => true),
            'cache'       => array('annotations' => true),
            'security'    => array('annotations' => true),
            'psr_message' => array('enabled' => false), // Defaults to true if the PSR-7 bridge is installed
        ));

你可以通过定义一个或多个 false 设定来关闭一些annotation和协议。

控制器的Annotations
---------------

Annotations are a great way to easily configure your controllers, from the
routes to the cache configuration.

Even if annotations are not a native feature of PHP, it still has several
advantages over the classic Symfony configuration methods:

* Code and configuration are in the same place (the controller class);
* Simple to learn and to use;
* Concise to write;
* Makes your Controller thin (as its sole responsibility is to get data from
  the Model).

.. tip::

   If you use view classes, annotations are a great way to avoid creating
   view classes for simple and common use cases.

Annotation 用来配置你的控制器是非常棒的方法，从路由到缓存配置。

甚至即使 annotation 不是PHP的特性，它仍然比传统的Symfony配置方法有几个优越性：

* 代码和配置都在同一个地方（控制器类里）；
* 学习和使用都很方便；
* 书写简明；
* 为你的控制器减肥（因为它全权负责从模型得到数据）。

.. tip::

   If you use view classes, annotations are a great way to avoid creating
   view classes for simple and common use cases.

   如果你使用显示类，为简单化和一般化，annotation是规避创建显示类的一个很棒的方法。

The following annotations are defined by the bundle:

下边的 annotation 是由此 bundle 定义的：

.. toctree::
   :maxdepth: 1

   annotations/routing
   annotations/converters
   annotations/view
   annotations/cache
   annotations/security

这个示例在操作里展示了所部可得的 annotation ::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Cache;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Method;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Security;

    /**
     * @Route("/blog")
     * @Cache(expires="tomorrow")
     */
    class AnnotController extends Controller
    {
        /**
         * @Route("/")
         * @Template
         */
        public function indexAction()
        {
            $posts = ...;

            return array('posts' => $posts);
        }

        /**
         * @Route("/{id}")
         * @Method("GET")
         * @ParamConverter("post", class="SensioBlogBundle:Post")
         * @Template("SensioBlogBundle:Annot:show.html.twig", vars={"post"})
         * @Cache(smaxage="15", lastmodified="post.getUpdatedAt()", etag="'Post' ~ post.getId() ~ post.getUpdatedAt()")
         * @Security("has_role('ROLE_ADMIN') and is_granted('POST_SHOW', post)")
         */
        public function showAction(Post $post)
        {
        }
    }

因为 ``showAction`` 方法遵循了一些协议，你可以省略一些 annotation::

    /**
     * @Route("/{id}")
     * @Cache(smaxage="15", lastModified="post.getUpdatedAt()", ETag="'Post' ~ post.getId() ~ post.getUpdatedAt()")
     * @Security("has_role('ROLE_ADMIN') and is_granted('POST_SHOW', post)")
     */
    public function showAction(Post $post)
    {
    }


路由需要在另外的路由资源导入来开启，例如：

.. code-block:: yaml

    # app/config/routing.yml

    # import routes from a controller directory
    annot:
        resource: "@AnnotRoutingBundle/Controller"
        type:     annotation

查看 :ref:`Annotated Routes Activation<frameworkextra-annotations-routing-activation>` 了解更多。

PSR-7 支持
--------

SensioFrameworkExtraBundle provides support for HTTP messages interfaces defined
in `PSR-7`_. It allows to inject instances of ``Psr\Http\Message\ServerRequestInterface``
and to return instances of ``Psr\Http\Message\ResponseInterface`` in controllers.

To enable this feature, `the HttpFoundation to PSR-7 bridge`_ and `Zend Diactoros`_ must be installed:

SensioFrameworkExtraBundle 提供了对以 `PSR-7`_ 对 HTTP message 接口的定义的支持。
它允许注射 ``Psr\Http\Message\ServerRequestInterface`` 的实例，并在控制器返回
``Psr\Http\Message\ResponseInterface`` 的实例。

要开启这个特性，必须安装 `the HttpFoundation to PSR-7 bridge`_ 和 `Zend Diactoros`_

.. code-block:: bash

    $ composer require symfony/psr-http-message-bridge zendframework/zend-diactoros

然后，PSR-7 message 就可以在控制器直接使用了，就像下边的代码片段一样::

    namespace AppBundle\Controller;

    use Psr\Http\Message\ServerRequestInterface;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;
    use Zend\Diactoros\Response;

    class DefaultController extends Controller
    {
        public function indexAction(ServerRequestInterface $request)
        {
            // Interact with the PSR-7 request

            $response = new Response();
            // Interact with the PSR-7 response

            return $response;
        }
    }

注意在内部，Symfony总是使用 :class:`:`Symfony\\Component\\HttpFoundation\\Request`
和 :class:`Symfony\\Component\\HttpFoundation\\Response` 实例。

.. _`SensioFrameworkExtraBundle`: https://github.com/sensiolabs/SensioFrameworkExtraBundle
.. _`PSR-7`: http://www.php-fig.org/psr/psr-7/
.. _`the HttpFoundation to PSR-7 bridge`: https://github.com/symfony/psr-http-message-bridge
.. _`Zend Diactoros`: https://github.com/zendframework/zend-diactoros
