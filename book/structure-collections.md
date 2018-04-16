Implementing typed collections
==============================

For stricter type hinting and interfaces it could be useful to implement typed collections to put your models and other same-typed
classes into.

As an example, we'll assume we have a `Post` class:

```php
class Post
{
    private $title;

    public function __construct($title)
    {
        $this->title = $title;
    }

    public function getTitle()
    {
        return $this->title;
    }
}
```

A simple typed immutable collection could be implemented like the following:

```php
class PostCollection implements \Countable, \IteratorAggregate
{
    private $data;

    public function __construct(array $data)
    {
        foreach ($data as $item) {
            if (!$item instanceof Post) {
                throw new \InvalidArgumentException('All items should be instance of Post class.');
            }
        }
        $this->data = $data;
    }

    public function count()
    {
        return count($this->data);
    }

    public function getIterator()
    {
        return new \ArrayIterator($this->data);
    }
}
```

That's it. Now you can use it like the following:

```php
$data = [new Post('post1'), new Post('post 2')];
$collection = new PostCollection($data);

foreach ($collection as $post) {
    echo $post->getTitle();
}
```

The main pro besides type checking in the constructor is about interfaces. With typed collections you may explicitly require
a set of items of a certain class:

```php
interface FeedGenerator
{
    public function generateFromPosts(PostsCollection $posts);
}

// instead of

interface FeedGenerator
{
    public function generateFromPosts(array $posts);
}
```
