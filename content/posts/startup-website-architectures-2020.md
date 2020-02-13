+++
title = "Startup Website architectures in 2020."
description = ""
author = "Ben George"
type = ["posts","post"]
tags = [

    "Startup",
    "Website",
    "Architecture",
]
date = "2020-01-10"
categories = [
    "Development",
    "Startups",
]

+++


What works today won’t necessarily be right for tomorrow, for startups that’s a mindset, the MVP way of building something shippable fast. But as the business grows and changes, your requirements change and your tech needs to also change.

## An idea. 

We need a website with some basic functionality, one of the founding team knows how to code (even at a basic level) and wants to put something together that is quick to build, cheap to host, SEO friendly, scalable and fast.

**Static site** ( HTML, CSS, JS) <br />
Or <br />
**Static site generators** <br />
    Jekyll <br />
    Hugo (Written in GO, superfast)

**Hosting:** AWS S3 with Cloudfront <br />
**CI:** Circle CI <br />
**Version Control:** Github

At this point things are very simple, changes to the websites involve a simple commit and push, CircleCI (or equivalent) has been setup to perform builds and handle automated deploys. Pull requests are raised from the develop branch, when reviewed and approved they are merged into the production branch, built and deployed.


## Angel to Seed
*Headcount: 3-10*

Changes are requested more frequently, but not enough that the current engineering team are motivated to provide any self serve solutions yet. Instead a few of the team are briefed on how to work with Markdown formatting and are given permissions to push these changes into a development branch in git. They enjoy picking up these new skills, pull requests are small, simple and quick and provide some release process.


## Seed to Series A
*Headcount: 10-30*

Now things are starting to get tricky, product and marketing all want to be able to make changes without involving engineering. The business also wants greater accountability for changes and process control. 

At this point you need to provide tools for non technical users to make safe changes.

But wait, I really don’t want to break or complicate the simple well architected website that we’ve built. Time for a headless CMS, recently i’ve used both Butter CMS & Contentful with successful results.

### What is a Headless CMS?
A headless CMS is a back-end only content management system (CMS) built from the ground up as a content repository that makes content accessible via a RESTful API for display on any device.

### Flexible Changes
A common request is for flexible changes using a ‘drag and drop’ interface. These changes I would rather route this through design and UX. In my experience, it's not a good idea to let product/marketing loose on page layout/design on the fly. A middle ground solution to this is to develop a few templates for page content that can be used. This way there speed, flexibility and control over design changes.

### Landing Pages
An extreme case is landing pages, often there is a use case for these to be constantly changed and optimised. To separate that concern I would use [Unbounce](https://unbounce.com/) for targeted landing pages that are highly customisable and outside the scope of the rest of the website.

## My 2020 go to Architecture:

**App:** VueJS / React <br />
**CMS:** Contentful / NUXT (For static caching) <br />
**Hosting:** AWS/cloudfront/Cloudflare/Netlify <br />
**CI:** Circle CI <br />
**Version control:** Github/Gitlab <br />
Deployment, single click push button deployment


![cloudfront-origin-domain-identity](/posts/post-startup-website-architectures-2020/startup-website-architectures-2020.png)


