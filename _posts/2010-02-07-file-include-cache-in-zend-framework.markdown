--- 
layout: post
title: File include cache in Zend Framework
wordpress_id: 223
wordpress_url: http://blog.elinkmedia.net.au/?p=223
date: 2010-02-07 13:12:18 +11:00
---
Another box of tissue gone ... runny nose syndrome continues ... still cannot type or think ...

Let me now blog something simple, but handy.

Problem ... Zend Framework uses many types of plugins, how do we efficiently tell the application where to find the plugin files? For example, when we use a view helper, should our application find it in the Zend library directory? Should our application find it in the application view helpers directory? Or should the application find it at our own custom library directory?

Fix ... it's easy.

Make the following extra line of config in your application config file

[code]cache.classFileIncludeCache   = APPLICATION_PATH &quot;/../data/pluginLoaderCache.php&quot;[/code]

Add the following code to your Bootstrap.php

[php]
  /**
     * Setup include file cache to increase performance
     *
     * @return void
     * @author Jim Li
     */
    protected function _initFileInlcudeCache()
    {
        $classFileIncCacheOptions = $this-&gt;getOption('cache');
        $classFileIncCache = $classFileIncCacheOptions['classFileIncludeCache'];

        if(file_exists($classFileIncCache)) {
            include_once $classFileIncCache;
        }
        Zend_Loader_PluginLoader::setIncludeFileCache($classFileIncCache);
    }
[/php]

Attention: make sure the "/data/" directory is writable by your web server.

Reference
<ul>
	<li><a href="http://framework.zend.com/manual/en/zend.loader.pluginloader.html#zend.loader.pluginloader.performance.example" target="_blank">http://framework.zend.com/manual/en/zend.loader.pluginloader.html#zend.loader.pluginloader.performance.example</a></li>
</ul>
