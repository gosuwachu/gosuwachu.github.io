---
layout: post
title: 'UITableView controller'
date: 2014-01-12 21:00:49
---
`UITableView` can cause containing it view controller to grow significantly by implementing it's delegates `UITableViewDataSource` and `UITableViewDelegate`. `UITableView` controller is a solution that we come up with recently, as we were modernizing our code base to make it more modular, extensible and easier to maintain.
There are several problems when view controller implements `UITableView`'s delegates directly:

* view controller grows by couple of hundred lines (depending how complex are your views)
* `UITableView` and it's content is not reusable in other parts of the app
* difficult to modify, if you want to add sections or rows that are not part of your model, but rather part of presentation layer (for example, if you want to show featured articles in the additional section or last row should shows *loading...* when app is loading more elements for that list)
* if model is not compatible with `UITableView` (like for example hierarchy of objects that you cannot map directly to `UITableViewDataSource` methods) you can end up mixing view controller's code with logic that transforms your model to something that can be displayed as a list
* not unit testable

Instead we can implement something that I will call `UITableView` controller, that moves implementation of delegates to new class, and separates different responsibilities of displaying a list. Diagram below illustrates how it works:
![img](/assets/UITableView-contoller-TableController1.png)
`TableController` implements `UITableViewDelegate` and `UITableViewDataSource`, and delegates creating views for cells and sections to `ITableViewCellFacotry` and `ITableViewSectionFactory`,  it also reads data from `ITableViewModel` and `ITableViewSectionModel` to supply `UITableView` with information about number of rows and sections.
This implementation is simple and works great. In my case we had several different lists that we had to display on the screen, each list was backed by different model, but they all used the same cells to display the data. We also had cases when we have to display extra cell at the end of the section or create a whole new section that does not exist in your model, but is part of your presentation. In cases like that you can build layers of `ITableViewModel`, where each layer performs one transformation, for example:
![img](/assets/UITableView-contoller-TableViewModel-transformation.png)

{% highlight objective-c %}
BasicTableViewModel* basicModel = [[BasicTableViewModel alloc] initWithModel:model];

// notice that order in which we perform transformations make a difference:
// 1. load more in each section + additional section for featured content without load more
// 2. additional section for featured content + load more in each section
LoadMoreTableViewModel* loadMoreModel =
  [[LoadMoreTableViewModel alloc] initWithTableViewModel:basicModel ];
FeaturedTableViewModel* featuredModel =
  [[FeaturedTableViewModel alloc] initWithTableViewModel:loadMoreModel];

TableController *tableController = [[TableController alloc] init];
tableController.model = featuredModel;
{% endhighlight %}
