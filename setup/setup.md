---
label: Setup
layout: setup
order: 10000
icon: mortar-board
---

!!!success
**Welcome to the Textuality Setup Guide!** Here, you can find everything you need to know about setting up Textuality.
Setup should take no longer than 5 minutes.
!!!

# Setup

## Creating a Textuality Account

To get started with Textuality, you need to create an account. You can do this by visiting the [Textuality website](https://textuality.hdev.uk) and clicking on the "Sign Up" button. You will be asked to provide your email address and create a password. [Sign Up](https://textuality.hdev.uk/sign-up)

## Creating a Textuality Page

Creating a Textuality page is easy. You can do this by following these steps:

- In the page home, click on the "Create Page" button.
- Enter a title for your page.
- Enter a description for your page.
- On the dropdown menu, select the category for your page.
- Click on the "Create" button.

## Setting Up Textuality on Your Website

!!!warning
Before you can see content on your website you will need to create content on Textuality and **publish it**.
!!!

### Now that we have created our page. We are able to set up Textuality.

On your page dashboard, Click "Get Started".
This has everything we need to get setup.

!!!danger
**Your Textuality API key is your secret key. Do not share it with anyone.**
!!!

First set up your .env file with the following:

```env
TEXTUALITY_PAGE_ID=insert your page id here
TEXTUALITY_API_KEY=insert your api key here
```

## Page Setup

Once we have this setup, Lets make a page for all our content.

### General Blog Page
Lets call this /blog



```ts
"use client";
import { useEffect, useState } from "react";

export default function BlogPage() {
  const [blogs, setBlogs] = useState<{ results: any[] } | null>(null);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch("/api/textuality/full")
      .then((res) => res.json())
      .then((data) => {
        if (data.error) {
          setError(data.error);
        } else {
          setBlogs(data.blogs);
        }
      })
      .catch((err) => {
        setError(err.message);
      });
  }, []);

  return (
    <div className="w-full min-h-screen flex flex-col">
      <main className="flex-grow h-screen py-12">
        <div className="md:container mx-auto flex flex-col h-full px-4">
          <div className="flex flex-col gap-1">
            <h1 className="text-3xl font-bold text-start">Blogs</h1>
            <div>
              <a href="/work" className="text-neutral-400 text-sm">Powered by Textuality</a>
            </div>
          </div>
          <div className="md:grid md:grid-cols-3 lg:grid-cols-4 gap-4 mt-4">
            {error ? (
              <p className="text-red-500">Blog page will be operational in approx 2 days</p>
            ) : (
              blogs?.results?.map((blog, index) => (
              <BlogCard
                key={index}
                title={blog.title}
                authorpfp={blog.author.imageUrl}
                authorName={blog.author.firstName + " " + blog.author.lastName}
                date={new Date(blog._creationTime).toLocaleDateString()}
              />
              ))
            )}
          </div>
        </div>
      </main>
    </div>
  );
}

function BlogCard({ title, authorName, date, authorpfp }: { title: string; authorName: string; date: string; authorpfp: string }) {
  return (
    <div className="flex flex-col gap-4 border w-full px-4 py-4 rounded-md bg-white dark:bg-black/60">
      <div className="flex flex-col gap-5">
        <h1 className="text-2xl font-bold text-start">{title}</h1>
        <div className="flex flex-row gap-2 items-center">
          <div className="flex flex-row gap-2 items-center">
            <img
              src={authorpfp}
              alt="Author"
              className="h-7 border-2 w-7 p-0.5 rounded-full"
            />
            <p className="text-neutral-400 text-sm">{authorName}</p>
          </div>
          •
          <p className="text-neutral-400 text-sm">{date}</p>
        </div>
      </div>
    </div>
  );
}
```

This is a simple page that will show all the blogs that you have created on Textuality.

### Specific Blog Page

We will also make a specific page for our blog posts. This is where all the content for that one blog will be shown.

**For react we will call this /blog/[_blogid]**

## Getting our API ready
Textuality has a simple API that you can use to get your content.

Lets make a file called `/api/textuality/full/route.ts`

We will use the following code:
    
```ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(_req: NextRequest) {

    const token = process.env.TEXTUALITY_API_KEY;
    const pageid = process.env.TEXTUALITY_PAGE_ID;

    const response = await fetch("http://textuality.hdev.uk/api/content/full/{pageid}", {
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`
        }
    });
    const data = await response.json();
    if (data.error) {
        return NextResponse.json({ error: data.error }, { status: 500 });
    } else {
        return NextResponse.json({ blogs: data }, { status: 200 });
    }
}
```
# Finish
All the technical stuff is done. 🥳

Now you can start creating content on Textuality, Publish it and it will show up on your website.

[!button target="blank" text="Start making your content"](https://textuality.hdev.uk/)
