shopify, javascript, graphql, fetch

# Developer account

Create store using partner account for unlimited testing:

https://dulynote.myshopify.com/

# Send graphQL requests from javascript

    const response = await fetch(
      'https://dulynote.myshopify.com/api/graphql',
      //'http://localhost:8000/api/graphql',
      {
        method: 'post',
        headers: {
          'Content-Type': 'application/graphql',
          'X-Shopify-Storefront-Access-Token': 'f613f80a9dc8c091de42987ca7caca3b',
        },
        body: `
          {
            shop {
              products(first: 5) {
                edges {
                  node {
                    handle,
                    variants(first: 20) {
                      edges {
                        node {
                          sku
                          title
                          price
                          availableForSale
                        }
                      }
                    },
                  }
                }
                pageInfo {
                  hasNextPage
                }
              }
            }
          }
        `
      }
    )
    const parsedResponse = await response.json()
    return parsedResponse.data.shop.products.edges
