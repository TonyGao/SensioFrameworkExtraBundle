@Security
=========

.. caution::

    The ``@Security`` annotation only works as of Symfony 2.4.

    ``@Security`` annotation 只针对 Symfony 2.4 以上版本。

用法
-----

The ``@Security`` annotation restricts access on controllers

``@Security`` annotation 限定了控制器的准入::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Security;

    class PostController extends Controller
    {
        /**
         * @Security("has_role('ROLE_ADMIN')")
         */
        public function indexAction()
        {
            // ...
        }
    }

The expression can use all functions that you can use in the ``access_control``
section of the security bundle configuration, with the addition of the
``is_granted()`` function.

The expression has access to the following variables:

* ``token``: The current security token;
* ``user``: The current user object;
* ``request``: The request instance;
* ``roles``: The user roles;
* and all request attributes.

The ``is_granted()`` function allows you to restrict access based on variables
passed to the controller

这个表达式可以使用兼容 security bundle 配置里 ``access_controle`` 部分带有
``is_granted()`` 函数的全部函数。

这个表达式将进入下边的变量:

* ``token``: 当前 security token;
* ``user``: 当前 user 对象;
* ``request``: request实例；
＊ ``roles``: 用户角色；
＊ 以及全部请求属性。

``is_granted()`` 函数允许你严格按照传入给控制器的变量来做准入::

    /**
     * @Security("is_granted('POST_SHOW', post)")
     */
    public function showAction(Post $post)
    {
    }

Here is another example, making use of multiple functions in the expression

这里是另外一个例子，在表达式里使用多个函数::

    /**
     * @Security("is_granted('POST_SHOW', post) and has_role('ROLE_ADMIN')")
     */
    public function showAction(Post $post)
    {
    }

.. note::

    Defining a ``Security`` annotation has the same effect as defining an
    access control rule, but it is more efficient as the check is only done
    when this specific route is accessed. To create new access control
    rules, please refer to `the Security Voters page`_.

    定义一个 ``Security`` annotation 与定义一个准入控制规则有一样的效果，但它更高效，
    因为只有这个被设定路由被进入时才会进行检查动作。要创建新的准入控制规则，请参考
    `the Security Voters page`_。

.. tip::

    You can also add a ``@Security`` annotation on a controller class.

    你也可以在一个控制器类上添加一个 ``@Security`` annotation。

.. _`the Security Voters page`: http://symfony.com/doc/current/cookbook/security/voters_data_permission.html
