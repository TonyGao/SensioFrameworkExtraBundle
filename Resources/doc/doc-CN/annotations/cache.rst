@Cache
======

The ``@Cache`` annotation makes it easy to define HTTP caching headers for
expiration and validation.

`@Cache` annotation 让定义过期和有效Http缓存头非常的容易。

HTTP 过期策略
---------

``@Cache`` annotation 让定义HTTP缓存非常的容易 ::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Cache;

    /**
     * @Cache(expires="tomorrow", public=true)
     */
    public function indexAction()
    {
    }

You can also use the annotation on a class to define caching for all actions
of a controller::

你也可以在一个类上使用 annotation 来为一个控制器的所有操作定义缓存::

    /**
     * @Cache(expires="tomorrow", public=true)
     */
    class BlogController extends Controller
    {
    }

When there is a conflict between the class configuration and the method
configuration, the latter overrides the former::

当类配置和方法配置之间有冲突，后者覆盖前者::

    /**
     * @Cache(expires="tomorrow")
     */
    class BlogController extends Controller
    {
        /**
         * @Cache(expires="+2 days")
         */
        public function indexAction()
        {
        }
    }

.. note::

   The ``expires`` attribute takes any valid date understood by the PHP
   ``strtotime()`` function.

   ``expires``属性能够认出PHP ``strtotime()``函数所能理解的有效日期。

HTTP 有效策略
---------

``lastModified``和``ETag``属性管理着HTTP有效缓存头，``lastModified``添加一个
``Last-Modified``头到响应，``ETag``添加一个``ETag``头。

当响应没修改时两者都自动触发发回一个304响应的逻辑（这种情况，控制器**不会**被调用）::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Cache;

    /**
     * @Cache(lastModified="post.getUpdatedAt()", ETag="'Post' ~ post.getId() ~ post.getUpdatedAt().getTimestamp()")
     */
    public function indexAction(Post $post)
    {
        // 你的代码
        // 在状态码为304的情况不会被调用
    }

也可以粗鲁的用下边的代码做同样的事 ::

    public function myAction(Request $request, Post $post)
    {
        $response = new Response();
        $response->setLastModified($post->getUpdatedAt());
        if ($response->isNotModified($request)) {
            return $response;
        }

        // your code
    }

.. note::

    The ETag HTTP header value is the result of the expression hashed with the
    ``sha256`` algorithm.

    ETag HTTP header 值是用``sha256``哈希算法表示的结果。

属性
---

Here is a list of accepted attributes and their HTTP header equivalent:

这里有一个属性列表和它们的 HTTP header 等价表示:

======================================================================= ================================
Annotation                                                              Response Method
======================================================================= ================================
``@Cache(expires="tomorrow")``                                          ``$response->setExpires()``
``@Cache(smaxage="15")``                                                ``$response->setSharedMaxAge()``
``@Cache(maxage="15")``                                                 ``$response->setMaxAge()``
``@Cache(vary={"Cookie"})``                                             ``$response->setVary()``
``@Cache(public=true)``                                                 ``$response->setPublic()``
``@Cache(lastModified="post.getUpdatedAt()")``                          ``$response->setLastModified()``
``@Cache(ETag="post.getId() ~ post.getUpdatedAt().getTimestamp()")``    ``$response->setETag()``
======================================================================= ================================
