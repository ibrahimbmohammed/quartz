
```ts
import { NextApiRequest, NextApiResponse } from 'next';

import { getWebsiteThemeColors } from '@graphql/Query';

import { domainNameBasedOnEnv } from '@utils/helpers';

import { WebsiteDataQueryQuery, WebsiteDataQueryQueryVariables } from '@gentypes/index';

import getClient from 'apolloClient';

  

async function getDomainData(req: NextApiRequest, res: NextApiResponse) {

const { host } = req.headers;

let endResultToBeSent: string;

const devEnvHostname = process.env.NEXT_PUBLIC_DEV_HOSTNAME;

const finalhost = domainNameBasedOnEnv(host as string);

const isSubdomained = host?.split('.');

const isSubdomainedFirstItem = host?.split('.')[0];

const isSubdomainedFirstFinal = finalhost?.split('.')[0];

console.log('final host', finalhost);

console.log('final sub', isSubdomained);

console.log('final sub first', isSubdomainedFirstItem);

console.log('final sub first', isSubdomainedFirstFinal);

// if the first item in the subdomain is equal to the finalhost, it means no subdomain was passed

if (isSubdomainedFirstItem === isSubdomainedFirstFinal) {

// user is trying to route to the conference or summit page

endResultToBeSent = finalhost!;

console.log('num========>', 1);

} else if (isSubdomainedFirstItem === 'conference' || isSubdomainedFirstItem === ' summit') {

// if the second item in host array is similar to final host , then we dont have a subdomain

if ((isSubdomained || [])[1] === isSubdomainedFirstFinal) {

// code

endResultToBeSent = finalhost!;

console.log('num========>', 2);

} else if (devEnvHostname && (isSubdomained ?? ' ')?.length <= 2) {

endResultToBeSent = `${finalhost}`;

console.log('num========>', 3.5);

} else {

endResultToBeSent = `${(isSubdomained || '')[1]}.${finalhost}`;

console.log('num========>', 3);

}

  

// this is for when an actual subdomain ,

} else if (isSubdomainedFirstItem !== isSubdomainedFirstFinal) {

// but can be wrong , typo or non existing record in the database

  

if (devEnvHostname && isSubdomained?.length === 1) {

endResultToBeSent = finalhost!;

console.log('num========>', 4);

} else {

console.log('num========>', 5);

endResultToBeSent = `${(isSubdomained || '')[0]}.${finalhost}`;

}

}

  

// i can get a subdomain

// have to filter out summit and conference subdomains

// what if i have four subdomains

// conference.enugu.niwe.org.ng

// thinking about it most subdomian would be four or more

// handle fallbacks

// when error during type a subdomian.

  

const client = getClient;

try {

const result = await client.query<WebsiteDataQueryQuery, WebsiteDataQueryQueryVariables>({

query: getWebsiteThemeColors,

variables: { domain_name: endResultToBeSent! },

});

  

if (result.data) {

const { website } = result.data;

  

res.status(200).json({ website });

}

} catch (error) {

res.status(500).json({

message: 'Something went wrong',

error,

});

}

}

  

export default getDomainData;
```