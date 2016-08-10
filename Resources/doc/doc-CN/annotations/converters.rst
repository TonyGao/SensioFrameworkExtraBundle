@ParamConverter
===============

用法
----

The ``@ParamConverter`` annotation calls *converters* to convert request
parameters to objects. These objects are stored as request attributes and so
they can be injected as controller method arguments

``@ParamConverter`` annotation 调用 *converters* 来转换请求参数到对象。
这些对象作为请求属性存储，因此它们可以作为方法参数注射到控制器::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;

    /**
     * @Route("/blog/{id}")
     * @ParamConverter("post", class="SensioBlogBundle:Post")
     */
    public function showAction(Post $post)
    {
    }

Several things happen under the hood:

* The converter tries to get a ``SensioBlogBundle:Post`` object from the
  request attributes (request attributes comes from route placeholders -- here
  ``id``);

* If no ``Post`` object is found, a ``404`` Response is generated;

* If a ``Post`` object is found, a new ``post`` request attribute is defined
  (accessible via ``$request->attributes->get('post')``);

* As for other request attributes, it is automatically injected in the
  controller when present in the method signature.

If you use type hinting as in the example above, you can even omit the
``@ParamConverter`` annotation altogether

后台发生的几件事:

* 这个转换器尝试从请求属性得到一个``SensioBlogBundle:Post``对象（请求属性来自路由
占位符，这里是``id``）；

* 如果没发现``Post``对象，会生成一个``404``响应；

* 如果找到一个``Post``对象，一个新的``post``请求属性会被定义。
（通过 ``$request->attributes->get('post'）``访问）；

* 至于其他请求属性，它会在方法里呈现时自动注射仅控制器。

如果你使用上边例子那样的类型暗示，你甚至完全可以省略``@ParamConverter`` annotation::

    // automatic with method signature
    public function showAction(Post $post)
    {
    }

.. tip::

    You can disable the auto-conversion of type-hinted method arguments feature
    by setting the ``auto_convert`` flag to ``false``:

    你可以通过设定``auto_convert``标记为``false``来关闭类型暗示方法参数特性的自动转换:

    .. configuration-block::

        .. code-block:: yaml

            # app/config/config.yml
            sensio_framework_extra:
                request:
                    converters: true
                    auto_convert: false

        .. code-block:: xml

            <sensio-framework-extra:config>
                <request converters="true" auto-convert="true" />
            </sensio-framework-extra:config>

To detect which converter is run on a parameter the following process is run:

* If an explicit converter choice was made with
  ``@ParamConverter(converter="name")`` the converter with the given name is
  chosen.

* Otherwise all registered parameter converters are iterated by priority. The
  ``supports()`` method is invoked to check if a param converter can convert
  the request into the required parameter. If it returns ``true`` the param
  converter is invoked.

要探测哪个转换器被用来运行在一个参数上，执行下边的过程：

* 如果一个显示的转换器选项被定义了，以``@ParamConverter(converter="name")``
会选择给定名字的转换器。

*否则全部登记的参数转换器会通过优先级进行交互。``supports()``方法会被调用来检查是否一个
参数转换器可以用来转换请求到所需的参数。如果它返回``true``这个参数转换器会被调用。

内置转换器
-----

The bundle has two built-in converters, the Doctrine one and a DateTime
converter.

此bundle有两个内置转换器，Doctrine一个和一个DateTime转换器。

Doctrine 转换器
~~~~~~~~~~~~

Converter Name: ``doctrine.orm``

The Doctrine Converter attempts to convert request attributes to Doctrine
entities fetched from the database. Two different approaches are possible:

- Fetch object by primary key.
- Fetch object by one or several fields which contain unique values in the
  database.

The following algorithm determines which operation will be performed.

- If an ``{id}`` parameter is present in the route, find object by primary key.
- If an option ``'id'`` is configured and matches route parameters, find object by primary key.
- If the previous rules do not apply, attempt to find one entity by matching
  route parameters to entity fields. You can control this process by
  configuring ``exclude`` parameters or a attribute to field name ``mapping``.

By default, the Doctrine converter uses the *default* entity manager. This can
be configured with the ``entity_manager`` option

转换器名称： ``doctrine.orm``

Doctrine转换器尝试着转换请求属性到从数据库取得的Doctrine实体。可能的两个不同的方式：

- 通过主键获取对象。
- 在数据库里通过一个或几个包含唯一值的字段获取对象。

下边的算法判断执行哪个操作。

- 如果路由里有一个``{id}``参数呈现出来了，就通过主键找对象。
- 如果一个选项 ``'id'`` 被配置了，并且符合路由参数，就通过主键找对象。
- 如果前边的规则都不符合，试着将符合路由参数到实体字段的方法找一个实体。
  你可以通过配置``exclude``参数或者一个属性到字段名称的``mapping``
  来控制这个过程。

默认，Doctrine转换器使用 *default* entity manager(实体管理器)。这可以通过
``entity_manager``选项进行配置::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;

    /**
     * @Route("/blog/{id}")
     * @ParamConverter("post", class="SensioBlogBundle:Post", options={"entity_manager" = "foo"})
     */
    public function showAction(Post $post)
    {
    }

If the placeholder does not have the same name as the primary key, pass the ``id``
option

如果没有与占位符同名的主键，传入``id``选项::

    /**
     * @Route("/blog/{post_id}")
     * @ParamConverter("post", class="SensioBlogBundle:Post", options={"id" = "post_id"})
     */
    public function showAction(Post $post)
    {
    }

.. tip::

   The ``id`` option specifies which placeholder from the route gets passed to the repository
   method used. If no repository method is specified, ``find()`` is used by default.

This also allows you to have multiple converters in one action

   ``id``选项设定了哪个来自路由的占位符会被传入仓库方法使用。如果没有指定仓库方法，默认使用``find()``。

也允许你在一个操作里有多个转换器::


    /**
     * @Route("/blog/{id}/comments/{comment_id}")
     * @ParamConverter("comment", class="SensioBlogBundle:Comment", options={"id" = "comment_id"})
     */
    public function showAction(Post $post, Comment $comment)
    {
    }

In the example above, the ``$post`` parameter is handled automatically, but ``$comment`` is
configured with the annotation since they can not both follow the default convention.

If you want to match an entity using multiple fields use the ``mapping`` hash
option: the key is route placeholder name and the value is the Doctrine
field name

在上边的例子里，``$post``参数是自动处理的，但``$comment``是通过annotation配置的，因为它两不是都能遵循
默认规则转换。

如果你想要符合一个实体的多个字段条件，使用``mapping``哈希选项：key是路由占位符名称，value是Doctrine字段
名称::

    /**
     * @Route("/blog/{date}/{slug}/comments/{comment_slug}")
     * @ParamConverter("post", options={"mapping": {"date": "date", "slug": "slug"}})
     * @ParamConverter("comment", options={"mapping": {"comment_slug": "slug"}})
     */
    public function showAction(Post $post, Comment $comment)
    {
    }

If you are matching an entity using several fields, but you want to exclude a
route parameter from being part of the criteria

如果你使用多字段符合了一个实体，但你想从这个标准里排除一个路由参数::

    /**
     * @Route("/blog/{date}/{slug}")
     * @ParamConverter("post", options={"exclude": {"date"}})
     */
    public function showAction(Post $post, \DateTime $date)
    {
    }

If you want to specify the repository method to use to find the entity (for example,
to add joins to the query), you can add the ``repository_method`` option

如果你像要设定仓库方法用来寻找实体（例如，给查询添加joins），你可以添加``repository_method``选项::

    /**
     * @Route("/blog/{id}")
     * @ParamConverter("post", class="SensioBlogBundle:Post", options={"repository_method" = "findWithJoins"})
     */
    public function showAction(Post $post)
    {
    }

The specified repository method will be called with the criteria in an ``array``
as parameter. This is a good fit with Doctrine's ``findBy`` and ``findOneBy``
methods.

There are cases where you want to you use your own repository method and you
want to map the criteria to the method signature. This is possible when you set
the ``map_method_signature`` option to true. The default is false

设定的仓库方法将被按照带有一个``array``参数的标准调用。这对Doctrine的``findBy``和
``findOneBy``特别适合。

当你想要使用你自己的仓库方法，并想要映射标准到方法签名里。可以设定``map_method_signature``
选项为true。默认是false::

    /**
     * @Route("/user/{first_name}/{last_name}")
     * @ParamConverter("user", class="AcmeBlogBundle:User", options={
     *    "repository_method" = "findByFullName",
     *    "mapping": {"first_name": "firstName", "last_name": "lastName"},
     *    "map_method_signature" = true
     * })
     */
    public function showAction(User $user)
    {
    }

    class UserRepository
    {
        public function findByFullName($firstName, $lastName)
        {
            ...
        }
    }

.. tip::

   When ``map_method_signature`` is ``true``, the ``firstName`` and
   ``lastName`` parameters do not have to be Doctrine fields.

   当 ``map_method_signature``是``true``，``firstName``和``lastName``
   参数不必是Doctrine字段。

DateTime 转换器
~~~~~~~~~~~~

Converter Name: ``datetime``

The datetime converter converts any route or request attribute into a datetime
instance

转换器名称: ``datetime``

datetime转换器转换任意路由或请求属性到一个datetime实例::


    /**
     * @Route("/blog/archive/{start}/{end}")
     */
    public function archiveAction(\DateTime $start, \DateTime $end)
    {
    }

By default any date format that can be parsed by the ``DateTime`` constructor
is accepted. You can be stricter with input given through the options

默认任意日期格式，只要它可以被``DateTime``构造器解析的都是许可的。你可以通过选项来给定
严格的格式::

    /**
     * @Route("/blog/archive/{start}/{end}")
     * @ParamConverter("start", options={"format": "Y-m-d"})
     * @ParamConverter("end", options={"format": "Y-m-d"})
     */
    public function archiveAction(\DateTime $start, \DateTime $end)
    {
    }

创建一个转换器
-------

All converters must implement the ``ParamConverterInterface``

全部的转换器必须实现自 ``ParamConverterInterface``::

    namespace Sensio\Bundle\FrameworkExtraBundle\Request\ParamConverter;

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\ParamConverter;
    use Symfony\Component\HttpFoundation\Request;

    interface ParamConverterInterface
    {
        function apply(Request $request, ParamConverter $configuration);

        function supports(ParamConverter $configuration);
    }

The ``supports()`` method must return ``true`` when it is able to convert the
given configuration (a ``ParamConverter`` instance).

The ``ParamConverter`` instance has three pieces of information about the annotation:

* ``name``: The attribute name;
* ``class``: The attribute class name (can be any string representing a class
  name);
* ``options``: An array of options.

The ``apply()`` method is called whenever a configuration is supported. Based
on the request attributes, it should set an attribute named
``$configuration->getName()``, which stores an object of class
``$configuration->getClass()``.

To register your converter service, you must add a tag to your service:

当可以转换给定的配置时（一个``ParamConverter``实例），``supports()``方法必须
返回``true``。

``ParamConverter``实例有三段关于 annotation 的信息:

* ``name``: 属性名称；
* ``class``: 属性类名称（可以是表示类名的任意字符串）；
* ``options``： 一个选项数组。

只要配置得到支持，``apply()``方法就会被调用。基于请求属性，它应该设定一个叫
``$configuration->getName()``属性，其存储了一个``$configuration->getClass()``
类的对象。

要登记你的转换器服务，你必须添加一个标签到你的服务：

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        services:
            my_converter:
                class:        MyBundle\Request\ParamConverter\MyConverter
                tags:
                    - { name: request.param_converter, priority: -2, converter: my_converter }

    .. code-block:: xml

        <service id="my_converter" class="MyBundle\Request\ParamConverter\MyConverter">
            <tag name="request.param_converter" priority="-2" converter="my_converter" />
        </service>

You can register a converter by priority, by name (attribute "converter"), or
both. If you don't specify a priority or a name, the converter will be added to
the converter stack with a priority of ``0``. To explicitly disable the
registration by priority you have to set ``priority="false"`` in your tag
definition.

.. tip::

   If you would like to inject services or additional arguments into a custom
   param converter, the priority shouldn't be higher than ``1``. Otherwise, the
   service wouldn't be loaded.

.. tip::

   Use the ``DoctrineParamConverter`` class as a template for your own converters.

你可以登记一个转换器，依照优先级，依照名称（属性"converter"），或两个都依照。如果你不设定优先级
或名称，转换器将以优先级``0``被添加到转换器栈。要显示的关闭以优先级登记，你得在你的tag定义里设定
``priority="false"``。

.. tip::

   如果你想要注射服务或者额外参数到一个自定义转换器，优先级应该高于``1``。否则，服务不会被加载。

.. tip::

   使用 ``DoctrineParamConverter``类作为一个模板给你自己的转换器。
