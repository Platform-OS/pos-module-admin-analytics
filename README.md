# Analytics

The analytics module provides a standard way to implement site analytics and event tracking (to be implemented).  


## Usage

TODO

## Hooks

Provides the `hook_analytics_widget` and the `hook_analytics_widgets_alter` hooks.  
Using these hooks other modules can provide their own statistics and analytics widgets to display on the admin analytics page.

Implements the `hook_permission` hook to add the `"admin.analytics.view"` permission to the system and `hook_admin_page` hooks to add the analytics dashboard page to the admin UI.

### hook_analytics_widget

Return value is an array of objects in the following format for each widget object:  

`id`: identifier, optional. It can be used to find and remove or modify the widget using the alter hook.  
`content`: HTML string, the content of the admin widget  
`weight`: number, optional: the order of the widget on the admin page  
`permission`: string, optional: the permission needed to view the widget. Default value: `"admin.analytics.view"`  
`access_callback`: path to access callback function, optional. Used for complex permission handling. For more details see the [can_do helper in the permission module](https://github.com/Platform-OS/pos-module-permission/blob/master/public/views/partials/lib/helpers/can_do.liquid)

## Examples

### "Registered users" widget using hook_analytics_widget 

```
{% comment %} Users widget {% endcomment %}
{% graphql users %}
query TotalUsers {
  users(per_page: 1) {
    total_entries
  }
}
{% endgraphql %}

{% capture users_content %}
  <div class="flex flex-col justify-between h-full">
    <div>
      <div class="uppercase">Registered users</div>
      <div class="font-bold">{{users.users.total_entries | json}}</div>
    </div>
    <div>
      <hr>
      <div class="flex justify-end">
        {% theme_render_rc 'components/atoms/button', weight: 'secondary', href: '/admin/users', content: 'View' %}
      </div>
    </div>
  </div>
{% endcapture %}

{% capture users_widget %}
  {% theme_render_rc 'components/atoms/card', content: users_content, classes: 'w-full sm:w-1/4' %}
{% endcapture %}

{% liquid
  assign widget = null | hash_merge: content: users_widget, id: 'users_widget', weight: 2, permission: 'users.view.all'
  assign result = '[]' | parse_json | add_to_array: widget
  return result
%}
```

## Versioning

```
git fetch origin --tags
npm version major | minor | patch
```
