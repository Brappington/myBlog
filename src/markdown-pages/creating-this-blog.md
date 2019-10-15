---
path: "/blog/creating-this-blog"
date: "10-10-2019"
title: "Creating this blog"
tags: ["Blog"]
---

Using this [tutorial](https://blog.bitsrc.io/how-to-build-a-blog-with-gatsby-and-boostrap-d1270212b3dc?gi=b9dce82cee2c) i was able to build this blog and customise it to my liking.
## Tech Stack

- Gatsby
- React
- GraphQL
- Plugins & Themes
- MDX / Markdown
- Bootstrap CSS
- Templates

## GatsbyJS

[GatsbyJS](https://www.gatsbyjs.org/)

Gatsby can get content from files and from network sources like an API endpoint. Content is obtained through GraphQL endpoints that you can message to display data in the way you want via templates. You can also build static page files that display content that doesn't change. 

You write the code to get the content and display it with templates. Then, you build the static web pages from the code you wrote. Plugins for Gatsby will provide the functionality you want.

## Features

- We will build a blog with some blog entries.
- The layout of the blog is a header from the top and content in the rest of the page.
- We will have a template to display all the blog entries with pagination with 5 posts each.
- We will have a categories page for displaying the list of articles with the given tag.
- We will use bootstrap for styling

## Set-up

Install the gatsby CLI:

    npm install -g gatsby-cli

Once installed, we run the CLI prgram to add the intial code for the website:

    gatsby new gatsby-blog

Then we instlall some add-on packages for Gatsby to do things like getting content from markdown files and displaying images. 

    npm i @mdx-js/mdx @mdx-js/react bootstrap gatsby-image gatsby-paginate gatsby-plugin-manifest gatsby-plugin-mdx gatsby-plugin-offline gatsby-plugin-react-helmet gatsby-plugin-sharp gatsby-remark-images gatsby-source-filesystem gatsby-transformer-remark gatsby-transformer-sharp jquery lodash popper.js react-helmet

The main packages on this list are gatsby-image for displaying images, gatsby-source-filesystem for getting the markdown files and display them in our templates. react-helmet for adding a head tag to our blog for including external CSS and script files. gatsby-transformer-remark is for converting markdown into HTML text. gatsby-transformer-sharp is for transforming images from various sources to display in our website.

## Implementation

### Header

In the `components` folder, we first work on `header.js`.

    import { Link } from "gatsby"
    import PropTypes from "prop-types"
    import React from "react"
    import { StaticQuery, graphql } from "gatsby"
    import _ from "lodash"
    import "./header.css"
    const Header = ({ siteTitle }) => (
      <nav className="navbar navbar-expand-md bg-dark navbar-dark">
        <a className="navbar-brand" href="#">
          {siteTitle}
        </a>
    <button
          className="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#collapsibleNavbar"
        >
          <span className="navbar-toggler-icon"></span>
        </button>
        <div className="collapse navbar-collapse" id="collapsibleNavbar">
          <ul className="navbar-nav">
            <li className="nav-item">
              <Link to="blog/page/1" className="nav-link">
                Home
              </Link>
            </li>
            <li className="nav-item dropdown">
              <a
                className="nav-link dropdown-toggle"
                href="#"
                id="navbarDropdown"
                role="button"
                data-toggle="dropdown"
                aria-haspopup="true"
                aria-expanded="false"
              >
                Categories
              </a>
              <div className="dropdown-menu" aria-labelledby="navbarDropdown">
                <StaticQuery
                  query={graphql`
                    query CategoryQuery {
                      allMarkdownRemark(limit: 2000) {
                        group(field: frontmatter___tags) {
                          fieldValue
                        }
                      }
                    }
                  `}
                  render={data => {
                    return data.allMarkdownRemark.group.map(g => {
                      return (
                        <Link
                          to={`tags/${g.fieldValue}`}
                          className="dropdown-item"
                        >
                          {_.capitalize(g.fieldValue)}
                        </Link>
                      )
                    })
                  }}
                />
              </div>
            </li>
          </ul>
        </div>
      </nav>
    )
    Header.propTypes = {
      siteTitle: PropTypes.string,
    }
    Header.defaultProps = {
      siteTitle: ``,
    }
    export default Header

This adds the Boostrap navigation bar to our app. We get the tags for all our posts and display them in a drop down in the navigation bar.

In `header.css` , to change the height of the navigation bar and change font sizes and margins, we put:

    .navbar-brand {  font-size: 20px;  margin-top: -20px;}
    nav.navbar {  padding-bottom: 0px;  height: 60px;}

### Layout

Create a file called layout.js in the `components` folder and add:

    /**
     * Layout component that queries for data
     * with Gatsby's useStaticQuery component
     *
     * See: https://www.gatsbyjs.org/docs/use-static-query/
     */
    import React from "react"
    import PropTypes from "prop-types"
    import { useStaticQuery, graphql } from "gatsby"
    import { Helmet } from "react-helmet"
    import Header from "./header"
    import "./layout.css"
    const Layout = ({ children }) => {
      const data = useStaticQuery(graphql`
        query SiteTitleQuery {
          site {
            siteMetadata {
              title
            }
          }
        }
      `)
    return (
        <>
          <Helmet>
            <title>Gatsby Blog</title>
            <script
              src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
              integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
              crossOrigin="anonymous"
            ></script>
            <script
              src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
              integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1"
              crossOrigin="anonymous"
            ></script>
            <script
              src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
              integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM"
              crossOrigin="anonymous"
            ></script>
            <link
              href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
              rel="stylesheet"
              integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
              crossOrigin="anonymous"
            />
          </Helmet>
          <Header siteTitle={data.site.siteMetadata.title} />
          <div
            style={{
              margin: `0 auto`,
              maxWidth: 960,
              padding: `0px 1.0875rem 1.45rem`,
              paddingTop: 0,
            }}
          >
            <main>{children}</main>
            <footer>
              © {new Date().getFullYear()}, Built with
              {` `}
              <a href="https://www.gatsbyjs.org">Gatsby</a>
            </footer>
          </div>
        </>
      )
    }
    Layout.propTypes = {
      children: PropTypes.node.isRequired,
    }
    export default Layout

This adds the `head` tag for  our HTML pages. We include the Boostrap CSS and code files and jQuery in the `Helmet` component, which will be converted to the `head` tag in our pages.

### Content

Next we add our content, create a `markdown-pages` folder in the `src` folder. Then we create some Markdown files. We add the following files, into the `markdown-pages` folder :

    creating-this-blog.md
    movie-search.md
    

- Add blogs

    Then in `creating-this-blog.md`, we add at the top:

        ---
        path: "/blog/creating-this-blog.md"
        date: "2019-05-04"
        title: "Creating this blog"
        tags: ["gatsby"]
        ---
        
In each markdown file, we have the metadata between the dotted lines. They will be obtained from the GraphQL queries and can be displayed on our pages, and we will display them.

### Static pages

We change default 404 pages by adding the top navigation bar. In 404.js , we replace the existing code with:

    import React from "react"
    import Layout from "../components/layout"
    import SEO from "../components/seo"
    import Header from "../components/header";
    
    const NotFoundPage = () => (
      <Layout>
        <SEO title="404: Not found" />
        <h1>NOT FOUND</h1>
        <p>You just hit a route that doesn&#39;t exist... the sadness.</p>
      </Layout>
    )
    export default NotFoundPage

Everything in the Layout component will be applied to this page, including the navigation bar and Bootstrap styles.

In index.js, to redirect to our blog template which we will create, we replace the existing code with:

    import React from "react"
    import Layout from "../components/layout"
    import SEO from "../components/seo"
    import { useEffect } from "react"
    import { navigate } from "gatsby"
    const IndexPage = () => {
      useEffect(() => {
        navigate("/blog/page/1")
      }, [])
    return (
        <Layout>
          <SEO title="Home" />
        </Layout>
      )
    }
    export default IndexPage

### Templates

We create a templates folder in the src folder to store our templates. In there, `create blog-list-template.js` and add:

    import React from "react"
    import { graphql, Link } from "gatsby"
    import Layout from "../components/layout"
    export default class BlogList extends React.Component {
      constructor(props) {
        super(props)
        this.state = { pageNumArray: [1] }
      }
    componentDidMount() {
        this.setPageArray()
      }
    componentWillReceiveProps() {
        this.setPageArray()
      }
    setPageArray() {
        const totalCount = this.props.data.allMarkdownRemark.totalCount
        const postsPerPage = this.props.data.allMarkdownRemark.pageInfo.perPage
        let pageNumArray = Array.from(
          { length: Math.ceil(totalCount / postsPerPage) },
          (v, i) => i + 1
        )
        this.setState({ pageNumArray })
      }
    render() {
        const posts = this.props.data.allMarkdownRemark.edges
        const pathNames = this.props.location.pathname.split("/")
        const page = pathNames[pathNames.length - 1]
        return (
          <Layout>
            {posts.map(({ node }) => {
              const title = node.frontmatter.title || node.fields.slug
              return (
                <div key={node.fields.slug}>
                  <h1>{title}</h1>
                  <b>Date Posted: {node.frontmatter.date}</b>
                  <div dangerouslySetInnerHTML={{ __html: node.html }} />
                </div>
              )
            })}
            <nav aria-label="Page navigation example">
              <ul className="pagination">
                {this.state.pageNumArray.map(p => (
                  <li className={`page-item ${page == p ? "active" : ""}`} key={p}>
                    <Link className={`page-link`} to={`blog/page/${p}`}>
                      {p}
                    </Link>
                  </li>
                ))}
              </ul>
            </nav>
          </Layout>
        )
      }
    }
    export const blogListQuery = graphql`
      query blogListQuery($skip: Int!, $limit: Int!) {
        allMarkdownRemark(
          sort: { fields: [frontmatter___date], order: DESC }
          limit: $limit
          skip: $skip
        ) {
          edges {
            node {
              fields {
                slug
              }
              frontmatter {
                title
                date
              }
              html
            }
          }
          pageInfo {
            perPage
          }
          totalCount
        }
      }
    `

This displays the content that are provided by the query that we wikll add in `gatsby-node.js`. The `blogListQuery` filters the content by running the query and returning the results. Then the results will be displayed in the components above. The `setPageArray` function gets the page numbers by getting the total number of pages and then setting it in the component state so that we can have links that go to the page, rendered by:

    {this.state.pageNumArray.map(p => (
       <li className={`page-item ${page == p ? "active" : ""}`} key={p}>
         <Link className={`page-link`} to={`blog/page/${p}`}>
         {p}
         </Link>
       </li>
    ))}

The blog posts are rendered by:

    {posts.map(({ node }) => {
      const title = node.frontmatter.title || node.fields.slug
      return (
        <div key={node.fields.slug}>
          <h1>{title}</h1>
          <b>Date Posted: {node.frontmatter.date}</b>
          <div dangerouslySetInnerHTML={{ __html: node.html }} />
          </div>
      )
    })}

Gatsby sanitizes HTML so we can display them by setting it directly with `dangerouslySetInnerHTML` . Next we make a template to display the posts with certain tags. To do this, we create tags.js in the templates folder and add:

    import React from "react"
    import PropTypes from "prop-types"
    import Layout from "../components/layout"
    import _ from "lodash"
    // Components
    import { Link, graphql } from "gatsby"
    const Tags = ({ pageContext, data }) => {
      const { tag } = pageContext
      const { edges, totalCount } = data.allMarkdownRemark
      const tagHeader = `${totalCount} post${
        totalCount === 1 ? "" : "s"
      } tagged with "${_.capitalize(tag)}"`
    return (
        <Layout>
          <h1>{tagHeader}</h1>
          {edges.map(({ node }) => {
            const { slug } = node.fields
            const { title, date } = node.frontmatter
            return (
              <div key={slug}>
                <h1>{title}</h1>
                <b>Date Posted: {date}</b>
                <div dangerouslySetInnerHTML={{ __html: node.html }} />
              </div>
            )
          })}
        </Layout>
      )
    }
    Tags.propTypes = {
      pageContext: PropTypes.shape({
        tag: PropTypes.string.isRequired,
      }),
      data: PropTypes.shape({
        allMarkdownRemark: PropTypes.shape({
          totalCount: PropTypes.number.isRequired,
          edges: PropTypes.arrayOf(
            PropTypes.shape({
              node: PropTypes.shape({
                frontmatter: PropTypes.shape({
                  title: PropTypes.string.isRequired,
                }),
                fields: PropTypes.shape({
                  slug: PropTypes.string.isRequired,
                }),
              }),
            }).isRequired
          ),
        }),
      }),
    }
    export default Tags
    export const pageQuery = graphql`
      query($tag: String) {
        allMarkdownRemark(
          limit: 2000
          sort: { fields: [frontmatter___date], order: DESC }
          filter: { frontmatter: { tags: { in: [$tag] } } }
        ) {
          totalCount
          edges {
            node {
              fields {
                slug
              }
              frontmatter {
                title
                date
              }
              html
            }
          }
        }
      }
    `

It works very similarly to blog-list-template.js . The GraphQL query at the bottom get the posts tagged with the given tag and then display them by running the component code above.

The metadata in our pages are in the `frontmatter` object of each entry.

### Config

Next in `gatsby-config.js` , to ensure all the packages we installed run, we replace to existing code with:

    module.exports = {
      siteMetadata: {
        title: `Gatsby Blog`,
        description: `Gatsby Blog`,
        author: `@gatsbyjs`,
      },
      plugins: [
        `gatsby-plugin-react-helmet`,
        {
          resolve: `gatsby-source-filesystem`,
          options: {
            name: `images`,
            path: `${__dirname}/src/images`,
          },
        },
        `gatsby-transformer-sharp`,
        `gatsby-plugin-sharp`,
        {
          resolve: `gatsby-plugin-mdx`,
          options: {
            gatsbyRemarkPlugins: [
              {
                resolve: `gatsby-remark-images`,
                options: {
                  maxWidth: 1200,
                },
              },
            ],
          },
        },
        {
          resolve: `gatsby-plugin-manifest`,
          options: {
            name: `gatsby-starter-default`,
            short_name: `starter`,
            start_url: `/blog/page/1`,
          },
        },
        // this (optional) plugin enables Progressive Web App + Offline functionality
        // To learn more, visit: https://gatsby.dev/offline
        // `gatsby-plugin-offline`,
        {
          resolve: `gatsby-source-filesystem`,
          options: {
            name: `markdown-pages`,
            path: `${__dirname}/src/markdown-pages`,
          },
        },
        `gatsby-plugin-react-helmet`,
        {
          resolve: `gatsby-transformer-remark`,
          options: {
            plugins: [
              {
                resolve: `gatsby-remark-images`,
                options: {
                  // It's important to specify the maxWidth (in pixels) of
                  // the content container as this plugin uses this as the
                  // base for generating different widths of each image.
                  maxWidth: 590,
                },
              },
            ],
          },
        },
      ],
    }

Finally, in `gatsby-node.js`, we replace the existing code with:

    const _ = require("lodash")
    const path = require("path")
    const { createFilePath } = require("gatsby-source-filesystem")
    exports.createPages = async ({ graphql, actions, reporter }) => {
      const { createPage } = actions
      const result = await graphql(
        `
          {
            postRemark: allMarkdownRemark(
              sort: { fields: [frontmatter___date], order: DESC }
              limit: 1000
            ) {
              edges {
                node {
                  fields {
                    slug
                  }
                }
              }
            }
            tagsGroup: allMarkdownRemark(limit: 2000) {
              group(field: frontmatter___tags) {
                fieldValue
              }
            }
          }
        `
      )
      if (result.errors) {
        reporter.panicOnBuild(`Error while running GraphQL query.`)
        return
      }
      // ...
      // Create blog-list pages
      const posts = result.data.postRemark.edges
      const postsPerPage = 5
      const numPages = Math.ceil(posts.length / postsPerPage)
      Array.from({ length: numPages }).forEach((_, i) => {
        createPage({
          path: `/blog/page/${i + 1}`,
          component: path.resolve("./src/templates/blog-list-template.js"),
          context: {
            limit: postsPerPage,
            skip: i * postsPerPage,
            numPages,
            currentPage: i + 1,
          },
        })
      })
    const tags = result.data.tagsGroup.group
      tags.forEach(tag => {
        createPage({
          path: `/tags/${_.kebabCase(tag.fieldValue)}/`,
          component: path.resolve("./src/templates/tags.js"),
          context: {
            tag: tag.fieldValue,
          },
        })
      })
    }
    exports.onCreateNode = ({ node, actions, getNode }) => {
      const { createNodeField } = actions
      if (node.internal.type === `MarkdownRemark`) {
        const value = createFilePath({ node, getNode })
        createNodeField({
          name: `slug`,
          node,
          value,
        })
      }
    }

In the file above, we have one query for getting all the posts and then create pages to display them in groups with the `postRemark` query below:

    postRemark: allMarkdownRemark(
      sort: { fields: [frontmatter___date], order: DESC }
        limit: 1000
      ) {
      edges {
        node {
          fields {
            slug
          }
      }
    }

We create the page files with:

    const posts = result.data.postRemark.edges
      const postsPerPage = 5
      const numPages = Math.ceil(posts.length / postsPerPage)
      Array.from({ length: numPages }).forEach((_, i) => {
        createPage({
          path: `/blog/page/${i + 1}`,
          component: path.resolve("./src/templates/blog-list-template.js"),
          context: {
            limit: postsPerPage,
            skip: i * postsPerPage,
            numPages,
            currentPage: i + 1,
          },
        })
      })

It looks through the returned results and them pipe the result entry to the template to generate the pages during build time.

The `tagsGroup` query below:

    tagsGroup: allMarkdownRemark(limit: 2000) {    group(field: frontmatter___tags) {      fieldValue    }}

get all the tags and then pipe the tags into the `tags.js` template where it gets the page content and creates the pages during build time with posts for a given tag in each page.

## Build the site

Now we are ready to build the static website by running `gatsby build` . The files should be built in the `public` folder. After that we install a HTTP server for serving the built static files. We will install the `http-server` Node.js packages. Run `npm i -g http-server` . Then run `http-server /path/to/gatsby-blog/public` .

### Time to customise

In order to make this blog my own i wanted to change a few things. Firstly instead of Gatsby blog showing in the the Navbar i wanted to put my name. To achieve this i changed the sites metadata in `gatsby-config.js`.

    siteMetadata: {
        title: `Jack Powell`,
        description: `Jack Powell's Blog`,
        author: `@gatsbyjs`,
      }

I also wanted to change the colour scheme to match my personal website. First i copy my custom properties from my personal website.

    :root {
      --bgColor: #282a36;
      --textColor: #f8f8f2;
      --headingColor: #50fa7b;
      --accentColor: #8be9fd;
      --pinkColor: #ff79c6;
      --orangeColor: #ffb86c;
      --purpleColor: #bd93f9;
      --redColor: #ff5555;
      --yellowColor:  #f1fa8c;
      --titleFont: "myFont";
      --regularFont: "myFont";
    }

Then i added the colour variables in `*` , `a`, 

    * {
      background-color: var(--bgColor);
      color: var(--textColor);
    }
    
    a {
      background-color: var(--bgColor);
      color: var(--textColor);
      -webkit-text-decoration-skip: objects;
    }
    mark {
      background-color: var(--yellowColor);
      color: var(--bgColor);
    }
    legend {
      box-sizing: border-box;
      color: var(--textColor);
      display: table;
      max-width: 100%;
      padding: 0;
      white-space: normal;
    }
    ::-webkit-input-placeholder {
      color: var(--textColor);
      opacity: 0.54;
    }
    ::-webkit-file-upload-button {
      -webkit-appearance: button;
      font: var(--textColor);
    }
    h1 {
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      padding-bottom: 0;
      padding-left: 0;
      padding-right: 0;
      padding-top: 0;
      margin-bottom: 1.45rem;
      font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
        Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
      font-weight: bold;
      text-rendering: optimizeLegibility;
      font-size: 2.25rem;
      line-height: 1.1;
      color: var(--headingColor);
    }
    h2 {
      color: var(--accentColor);
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      padding-bottom: 0;
      padding-left: 0;
      padding-right: 0;
      padding-top: 0;
      margin-bottom: 1.45rem;
      font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
        Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
      font-weight: bold;
      text-rendering: optimizeLegibility;
      font-size: 1.62671rem;
      line-height: 1.1;
    }
    h3 {
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      padding-bottom: 0;
      padding-left: 0;
      padding-right: 0;
      padding-top: 0;
      margin-bottom: 1.45rem;
      color: var(--orangeColor);
      font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
        Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
      font-weight: bold;
      text-rendering: optimizeLegibility;
      font-size: 1.38316rem;
      line-height: 1.1;
    }
    h4 {
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      padding-bottom: 0;
      padding-left: 0;
      padding-right: 0;
      padding-top: 0;
      margin-bottom: 1.45rem;
      color: var(--purpleColor);
      font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
        Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
      font-weight: bold;
      text-rendering: optimizeLegibility;
      font-size: 1rem;
      line-height: 1.1;
    }
    h5 {
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      padding-bottom: 0;
      padding-left: 0;
      padding-right: 0;
      padding-top: 0;
      margin-bottom: 1.45rem;
      color: var(--pinkColor);
      font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
        Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
      font-weight: bold;
      text-rendering: optimizeLegibility;
      font-size: 0.85028rem;
      line-height: 1.1;
    }
    
    pre {
      margin-left: 0;
      margin-right: 0;
      margin-top: 0;
      margin-bottom: 1.45rem;
      font-size: 0.85rem;
      line-height: 1.42;
      background-color: var(--textColor);
      color: var(--bgColor);
      border-radius: 3px;
      overflow: auto;
      word-wrap: normal;
      padding: 1.45rem;
    }