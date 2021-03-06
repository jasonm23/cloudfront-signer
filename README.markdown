# cloudfront-signer
## New Repository
As discussed in [#5](https://github.com/58bits/cloudfront-signer/pull/5) the new home for this gem is [leonelgalan/cloudfront-signer](https://github.com/leonelgalan/cloudfront-signer). The new repository, won't be a fork, allowing users to post issues. 

---

See the [ChangeLog](https://github.com/58bits/cloudfront-signer/blob/master/ChangeLog.markdown) for details of this release.

A fork and re-write of Dylan Vaughn's [signing gem](https://github.com/stlondemand/aws_cf_signer).

See Amazon docs for [Using a Signed URL to Serve Private Content](http://docs.amazonwebservices.com/AmazonCloudFront/latest/DeveloperGuide/index.html?PrivateContent.html)

This version uses all class methods and a configure method to set options.

Seperate helper methods exist for safe signing of urls and stream paths, each of which has slightly different requirements. For example, urls must not contain any spaces, whereas a stream path can. Also we might not want to html escape a url or path if it is being supplied to a JavaScript block or Flash object.

## Installation

The original gem was published as `aws_cf_signer`. Use `gem install aws_cf_signer` to install that version.

This gem has been publised as `cloudfront-signer`. Use `gem install cloudfront-signer` to install this gem.

Alternatively, place a copy of cloudfront-signer.rb (and the cloundfront-signer sub directory) in your lib directory.

In either case the signing class must be configured - supplying the path to a signing key, or supplying the signing key directly as a string along with the `key_pair_id`. Create the initializer by running:

```
bundle exec rails g cloudfront:install
```

and customizing the resulting `config/initializers/cloudfront-signer.rb` file.

### Generated `cloudfront-signer.rb`

    AWS::CF::Signer.configure do |config|
      config.key_path = '/path/to/keyfile.pem'
      # config.key = ENV.fetch('PRIVATE_KEY') # key_path not required if key supplied directly
      config.key_pair_id  = "XXYYZZ"
      config.default_expires = 3600
    end

## Usage

Call the class `sign_url` or `sign_path` method with optional policy settings.

    AWS::CF::Signer.sign_url 'http://mydomain/path/to/my/content'

or

    AWS::CF::Signer.sign_url 'http://mydomain/path/to/my/content', :expires => Time.now + 600

Streaming paths can be signed with the `sign_path` method.

    AWS::CF::Signer.sign_path 'path/to/my/content'

or

    AWS::CF::Signer.sign_path 'path/to/my/content', :expires => Time.now + 600


Both `sign_url` and `sign_path` have _safe_ versions that HTML encode the result allowing signed paths or urls to be placed in HTML markup. The 'non'-safe versions can be used for placing signed urls or paths in JavaScript blocks or Flash params.


### Custom Policies

See Example Custom Policy 1 at above AWS doc link

    url = AWS::CF::Signer.sign_url('http://d604721fxaaqy9.cloudfront.net/training/orientation.avi',
            :expires   => 'Sat, 14 Nov 2009 22:20:00 GMT',
            :resource => 'http://d604721fxaaqy9.cloudfront.net/training/*',
            :ip_range => '145.168.143.0/24'
    )

See Example Custom Policy 2 at above AWS doc link

    url = AWS::CF::Signer.sign_url('http://d84l721fxaaqy9.cloudfront.net/downloads/pictures.tgz',
            :starting => 'Thu, 30 Apr 2009 06:43:10 GMT',
            :expires   => 'Fri, 16 Oct 2009 06:31:56 GMT',
            :resource => 'http://*',
            :ip_range => '216.98.35.1/32'
    )

You can also pass in a path to a policy file. This will supersede any other policy options

    url = AWS::CF::Signer.sign_url('http://d84l721fxaaqy9.cloudfront.net/downloads/pictures.tgz', :policy_file => '/path/to/policy/file.txt')


## Patches/Pull Requests

* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it.
* Commit
* Send me a pull request. Bonus points for topic branches.

## Attributions

Parts of signing code taken from a question on Stack Overflow asked by Ben Wiseley, and answered by Blaz Lipuscek and Manual M:

* [http://stackoverflow.com/questions/2632457/create-signed-urls-for-cloudfront-with-ruby](http://stackoverflow.com/questions/2632457/create-signed-urls-for-cloudfront-with-ruby)
* [http://stackoverflow.com/users/315829/ben-wiseley](http://stackoverflow.com/users/315829/ben-wiseley)
* [http://stackoverflow.com/users/267804/blaz-lipuscek](http://stackoverflow.com/users/267804/blaz-lipuscek)
* [http://stackoverflow.com/users/327914/manuel-m](http://stackoverflow.com/users/327914/manuel-m)

Note: Dylan blazed a trail here - however, after several attempts, I was unable to contact Dylan in order to suggest that we combine our efforts to produce a single gem - hence the re-write and new gem here.


License
-------

cloudfront-signer is distributed under the MIT License, portions copyright © 2011 Dylan Vaughn, STL, Anthony Bouch
