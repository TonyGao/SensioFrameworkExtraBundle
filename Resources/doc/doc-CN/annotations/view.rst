@Template
=========

用法
-----

The ``@Template`` annotation associates a controller with a template name

``@Template`` annotation 通过一个模板名与控制器产生关联::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;

    /**
     * @Template("SensioBlogBundle:Post:show.html.twig")
     */
    public function showAction($id)
    {
        // get the Post
        $post = ...;

        return array('post' => $post);
    }

When using the ``@Template`` annotation, the controller should return an
array of parameters to pass to the view instead of a ``Response`` object.

当使用 ``@Template`` annotation时，控制器应该返回一个传入视图的参数数组，来代替返回
``Response`` 对象。

.. note::

    If you want to stream your template, you can make it with the following configuration

    如果你想要流化你的模板，你可以通过下边的配置做到::

        /**
         * @Template(isStreamable=true)
         */
        public function showAction($id)
        {
            // ...
        }


.. tip::
   If the action returns a ``Response`` object, the ``@Template``
   annotation is simply ignored.

   如果此操作返回一个 ``Response`` 对象，``@Template`` annotation 会简单的忽略。

If the template is named after the controller and action names, which is the
case for the above example, you can even omit the annotation value

如果模板被命名在控制器和操作名之后，如同上边例子那样，你甚至可以省略 annotation 值::

    /**
     * @Template
     */
    public function showAction($id)
    {
        // get the Post
        $post = ...;

        return array('post' => $post);
    }

.. note::

    If you are using PHP as a templating system, you need to make it
    explicit

    如果你正在使用 PHP 作为模板系统，你可以显示的表示它::

        /**
         * @Template(engine="php")
         */
        public function showAction($id)
        {
            // ...
        }

And if the only parameters to pass to the template are method arguments, you
can use the ``vars`` attribute instead of returning an array. This is very
useful in combination with the ``@ParamConverter`` :doc:`annotation
<converters>`

如果方法参数是传入模板的唯一参数，你可以用 ``var`` 属性代替返回一个数组。这与
``@ParamConverter`` :doc:`annotation <converters>` 结合非常有用::

    /**
     * @ParamConverter("post", class="SensioBlogBundle:Post")
     * @Template("SensioBlogBundle:Post:show.html.twig", vars={"post"})
     */
    public function showAction(Post $post)
    {
    }

which, thanks to conventions, is equivalent to the following configuration

多亏了协议，这等同于下边的配置::

    /**
     * @Template(vars={"post"})
     */
    public function showAction(Post $post)
    {
    }

You can make it even more concise as all method arguments are automatically
passed to the template if the method returns ``null`` and no ``vars``
attribute is defined

你甚至可以让它变得更简明，即在方法返回 ``null`` 而不是定义 ``vars`` 属性时全部方法参数都
自动传入模板::

    /**
     * @Template
     */
    public function showAction(Post $post)
    {
    }
