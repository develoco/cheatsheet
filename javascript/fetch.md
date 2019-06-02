fetch, fetchapi, javascript, nodejs

# Posting graphql

    const response = await fetch(
      'http://localhost:8000/api/graphql',
      {
        method: 'post',
        headers: {
          'Content-Type': 'application/graphql',
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

# Using nodejs

# Promises and await (async)

    async (token, projectId) => {
      const url = `${process.env.REACT_APP_BASE_URL}/v1/foo`
      const url2 = `${process.env.REACT_APP_BASE_URL}/v1/bar`
      const options = {
        headers: {
          'Content-Type': 'application/json',
        },
      }

      return await Promise.all(
        (await Promise.all([
          fetch(url, options),
          fetch(url2, options)
        ])
      ).map(response => response.json()))
    }
