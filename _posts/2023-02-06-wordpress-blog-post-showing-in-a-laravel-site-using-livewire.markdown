---
title: Wordpress blog post showing in a Laravel site using livewire
layout: post
---

~~~php
php artisan make:livewire latest-blog-posts
~~~

# LatestBlogPosts
~~~php
<?php

namespace App\Http\Livewire\Frontend\V2\FrontPage;

use Livewire\Component;
use Illuminate\Support\Facades\Http;
use Illuminate\Http\Client\ConnectionException;


class LatestBlogPosts extends Component
{
  public $posts = [];
  public function render()
  {
    return view('livewire.frontend.v2.front-page.latest-blog-posts');
  }
  public function mount()
  {
    $raw_posts = [];
    try {
      $response = Http::get('https://blog.bimafy.com/wp-json/wp/v2/posts?per_page=3&tags[]=91');
      $json_decoded_response = json_decode($response);
      $raw_posts = collect($json_decoded_response)->all();
    } catch(ConnectionException $e)
    {
      return false;
      report($e);
    }


    $posts = [];
    foreach ($raw_posts as $raw_post) {
      $image = isset($raw_post->yoast_head_json) ? ( isset($raw_post->yoast_head_json->og_image[0]) ? $raw_post->yoast_head_json->og_image[0]->url : '' ): '';
      $single_post = [
        'image' => $image,
        'url'   => $raw_post->link,
        'title' => $raw_post->title->rendered,
      ];
      $posts[] = $single_post;
    }
    $this->posts = $posts;
  }
}

~~~

# latest-blog-posts.blade.php

~~~html
<div>
  <div class="py-5 latest_blog_post_area">
    <div class="container">
      <div class="latest_blog_post_area_inner">
        <h2 class="pb-3 text-center">
          <a href="https://blog.bimafy.com/">
            Latest updates and news
          </a>
        </h2>
        <div class="row">
          @foreach ($posts as $single_blog_post)
            <div class="mb-3 col-md mb-md-0">
              <div class="pb-4 single_blog_post px-auto">
                <img class="img-fluid rounded-top" src="{{ $single_blog_post['image'] }}" alt="{{ $single_blog_post['title'] }}">
                <div class="p-4 single_blog_post_content">
                  <h1 class="h5">{{ $single_blog_post['title'] }}</h1>
                  <div>
                    <a target="_blank" href="{{ $single_blog_post['url'] }}" class="btn btn-accent">Read More</a>
                  </div>
                </div>
              </div>
            </div>
          @endforeach
        </div>
      </div>
    </div>
  </div>
</div>
~~~



