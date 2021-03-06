---
title: Gatsby Link
---

A `<Link>` component for Gatsby.

It's a wrapper around
[@reach/router's Link component](https://reach.tech/router/api/Link)
that adds enhancements specific to Gatsby. All props are passed through to @reach/router's `Link` component.

You can set the `activeStyle` or `activeClassName` prop to add styling
attributes to the rendered element when it matches the current URL.

Gatsby does per-route code splitting. This means that when navigating to a new
page, the code chunks necessary for that page might not be loaded. This is bad as
any unnecessary latency when changing pages should be avoided. So to avoid that,
Gatsby preloads code chunks and page data.

Preloading is triggered by a link entering the viewport; Gatsby uses
`Link`'s `innerRef` property to create a new InteractionObserver (on
supported browsers) to monitor visible links. This way, Gatsby only prefetches
code/data chunks for pages the user is likely to navigate to. You can also get
access to the link element by passing in a `innerRef` prop.

## How to use

In JavaScript:

```jsx
import React from "react"
import { Link } from "gatsby"

class Page extends React.Component {
  render() {
    return (
      <div>
        <Link
          to="/another-page/"
          activeStyle={{
            color: "red",
          }}
          innerRef={el => {
            this.myLink = el
          }}
        >
          Another page
        </Link>
      </div>
    )
  }
}
```

## Programmatic navigation

For cases when you can only use event handlers for navigation, you can use `push` or `replace`. `push` is a wrapper for `history.push` and `replace` wraps `history.replace`.

```jsx
import { push } from "gatsby"

render () {
  <div onClick={ () => push('/example')} role="link" tabIndex="0" onKeyUp={this.handleKeyUp}>
    <p>Example</p>
  </div>
}
```

Note that `push` was previously named `navigateTo`. `navigateTo` is deprecated in Gatsby v2.

## Prefixed paths helper

It is common to host sites in a sub-directory of a site. Gatsby let's you [set
the path prefix for your site](/docs/path-prefix/). After doing so, Gatsby's `<Link>` component will automatically handle constructing the correct URL in development and production.

For pathnames you construct manually, there's a helper function, `withPrefix` that prepends your path prefix in production (but doesn't during development where paths don't need prefixed).

```jsx
import { withPrefix } from "gatsby"

const IndexLayout = ({ children, location }) => {
  const isHomepage = location.pathname === withPrefix("/")

  return (
    <div>
      <h1>Welcome {isHomepage ? "home" : "aboard"}!</h1>
      {children}
    </div>
  )
}
```

## Use `<Link>` only for internal links!

This component is intended _only_ for links to pages handled by Gatsby. For links to pages on other domains or pages on the same domain not handled by the current Gatsby site, use the normal `<a>` element.

Sometimes you won't know ahead of time whether a link will be internal or not,
such as when the data is coming from a CMS.
In these cases you may find it useful to make a component which inspects the
link and renders either with Gatsby's `<Link>` or with a regular `<a>` tag
accordingly.

Since deciding whether a link is internal or not depends on the site in
question, you may need to customize the heuristic to your environment, but the
following may be a good starting point:

```jsx
import { Link as GatsbyLink } from "gatsby"

const Link = ({ children, to, ...other }) => {
  // Tailor the following test to your environment.
  // This example assumes that any internal link (intended for Gatsby)
  // will start with exactly one slash, and that anything else is external.
  const internal = /^\/(?!\/)/.test(to)

  // Use Gatsby Link for internal links, and <a> for others
  if (internal) {
    return (
      <GatsbyLink to={to} {...other}>
        {children}
      </GatsbyLink>
    )
  }
  return (
    <a href={to} {...other}>
      {children}
    </a>
  )
}

export default Link
```
