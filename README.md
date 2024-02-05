const functions = require('@google-cloud/functions-framework');
const GcmSynthetics = require('@google-cloud/synthetics-sdk-broken-links');

// Array of URLs to check
const urls = [
  'https://www.google.com',
  'https://www.example.com',
  // Add more URLs here as needed
];

functions.http('BrokenLinkChecker', (req, res) => {
  // Run the broken link check for each URL
  Promise.all(urls.map(url => {
    const options = {
      origin_uri: url,
      link_limit: 25,
      query_selector_all: 'a,img,script',
      wait_for_selector: '',
      get_attributes: ["href","src"],
      link_order: 'FIRST_N',
      link_timeout_millis: 30000,
      max_retries: 0,
      per_link_options: {}
    };
    return GcmSynthetics.runBrokenLinksHandler(options);
  }))
  .then(results => {
    // Process the results
    res.status(200).send(results);
  })
  .catch(error => {
    // Handle any errors
    res.status(500).send(error.message);
  });
});
