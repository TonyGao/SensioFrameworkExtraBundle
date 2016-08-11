@Route and @Method
==================

用法
-----

The ``@Route`` annotation maps a route pattern with a controller

``@Route`` annotation 在控制器里映射了一个路由模板::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;

    class PostController extends Controller
    {
        /**
         * @Route("/")
         */
        public function indexAction()
        {
            // ...
        }
    }

The ``index`` action of the ``Post`` controller is now mapped to the ``/``
URL. This is equivalent to the following YAML configuration:

``Post``控制器的``index``操作现在映射到了 ``/`` URL。这等同于下边的YAML配置:

.. code-block:: yaml

    blog_home:
        path:     /
        defaults: { _controller: SensioBlogBundle:Post:index }

Like any route pattern, you can define placeholders, requirements, and default
values

像任何路由模板一样，你可以定义占位符，需求和默认值::

    /**
     * @Route("/{id}", requirements={"id" = "\d+"}, defaults={"id" = 1})
     */
    public function showAction($id)
    {
    }

You can also define the default value for a placeholder with
the PHP default value

你也可以用PHP默认值为一个占位符定义默认值::

    /**
     * @Route("/{id}", requirements={"id" = "\d+"})
     */
    public function showAction($id = 1)
    {
    }

You can also match more than one URL by defining additional ``@Route``
annotations

你也可以通过定义额外的 ``@Route`` annotations 来匹配多个URL::

    /**
     * @Route("/", defaults={"id" = 1})
     * @Route("/{id}")
     */
    public function showAction($id)
    {
    }

.. _frameworkextra-annotations-routing-activation:

激活
----

The routes need to be imported to be active as any other routing resources
(note the ``annotation`` type):

路由需要引入并作为路由资源激活(注意 ``annotation``类型):

.. code-block:: yaml

    # app/config/routing.yml

    # import routes from a controller class
    post:
        resource: "@SensioBlogBundle/Controller/PostController.php"
        type:     annotation

You can also import a whole directory:

你也可以导入整个目录:

.. code-block:: yaml

    # import routes from a controller directory
    blog:
        resource: "@SensioBlogBundle/Controller"
        type:     annotation

As for any other resource, you can "mount" the routes under a given prefix:

至于任意其他资源,你可以在一个给定的前缀"挂载"路由:

.. code-block:: yaml

    post:
        resource: "@SensioBlogBundle/Controller/PostController.php"
        prefix:   /blog
        type:     annotation

路由名称
----------

A route defined with the ``@Route`` annotation is given a default name composed
of the bundle name, the controller name and the action name. That would be
``sensio_blog_post_index`` for the above example;

The ``name`` attribute can be used to override this default route name

用 ``@Route`` annotation 定义的路由被赋予一个由bundle名称、控制器名和操作名称组成的默认名。
对上边的例子来说那会是 ``sensio_blog_post_index``;

``name``属性可以用来覆盖这个默认的路由名称::

    /**
     * @Route("/", name="blog_home")
     */
    public function indexAction()
    {
        // ...
    }

路由前缀
------------

A ``@Route`` annotation on a controller class defines a prefix for all action
routes (note that you cannot have more than one ``@Route`` annotation on a
class)

控制器类上的 ``@Route`` annotation 为全部操作路由定义了一个前缀（注意，在一个类上不能有
多于一个的 ``@Route``）::

    /**
     * @Route("/blog")
     */
    class PostController extends Controller
    {
        /**
         * @Route("/{id}")
         */
        public function showAction($id)
        {
        }
    }

The ``show`` action is now mapped to the ``/blog/{id}`` pattern.

Route Method
------------

There is a shortcut ``@Method`` annotation to specify the HTTP method allowed
for the route. To use it, import the ``Method`` annotation namespace

``show``操作现在映射到了``/blog/{id}``模板。

路由方法
---------

有一个快捷方法 ``@Method`` annotation，用来设定此路由允许的HTTP方法。要用它，
需要导入 ``Method`` annotation 的命名空间::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Method;

    /**
     * @Route("/blog")
     */
    class PostController extends Controller
    {
        /**
         * @Route("/edit/{id}")
         * @Method({"GET", "POST"})
         */
        public function editAction($id)
        {
        }
    }

The ``edit`` action is now mapped to the ``/blog/edit/{id}`` pattern if the HTTP
method used is either GET or POST.

The ``@Method`` annotation is only considered when an action is annotated with
``@Route``.

如果使用了 HTTP GET 或 POST 方法， ``edit`` 操作现在映射到了 ``/blog/edit/{id}`` 模板。

``@Method`` annotation 只有当一个操作有 ``@Route`` annotation 时才会被考虑。

作为服务的控制器
---------------------

The ``@Route`` annotation on a controller class can also be used to assign the
controller class to a service so that the controller resolver will instantiate
the controller by fetching it from the DI container instead of calling ``new
PostController()`` itself

控制器类上的 ``@Route`` annotation 也可以用来赋予控制器类服务特性，因此控制器分解器实例化
控制器的过程，将通过从DI容器获取，以取代自己调用 ``new PostConntroller()``::

    /**
     * @Route(service="my_post_controller_service")
     */
    class PostController
    {
        // ...
    }
