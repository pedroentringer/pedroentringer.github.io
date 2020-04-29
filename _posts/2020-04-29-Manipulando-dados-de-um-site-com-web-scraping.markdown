---
layout: post
title:  "Manipulando dados de um site com Web Scraping"
date:   2020-04-29 15:30:00
categories: Nodejs
---

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:


{% highlight javascript %}
import cheerio from 'cheerio'
import parseTable from './parseTable'
import puppeteer from 'puppeteer'

'use strict'

/** @type {typeof import('@adonisjs/lucid/src/Lucid/Model')} */
const Model = use('Model')

/** @type {import('@adonisjs/framework/src/Hash')} */
const Hash = use('Hash')

class User extends Model {
  static boot () {
    super.boot()

    this.addHook('beforeCreate', 'UserHook.sendNewUserEmail')

    this.addHook('beforeSave', async userInstance => {
      if (userInstance.dirty.password) {
        userInstance.password = await Hash.make(userInstance.password)
      }
    })

  }

  tokens () {
    return this.hasMany('App/Models/Token')
  }

  branches () {
    return this.belongsToMany('App/Models/Branch').pivotTable('user_branches')
  }

  accessGroups () {
    return this.belongsToMany('App/Models/GroupAccess').pivotTable('user_group_accesses').pivotModel('App/Models/UserGroupAccess')
  }

}

module.exports = User
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
