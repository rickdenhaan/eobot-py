Eobot
=====

Configuration
-------------

To use the API, you need to set up the authentication credentials for the Eobot.com account you'll be querying. Use the
``get_config()`` method to get access to the configuration object used by the ``eobot`` module:

    >>> import eobot
    >>> config = eobot.get_config()

This will return an ``EobotConfig`` object where you can set the following values:

    >>> config.set_user_id(12345)             # set the Eobot.com user ID
    >>> config.set_email('user@example.com')  # set the Eobot.com user login email address
    >>> config.set_password('password')       # set the Eobot.com user login password
    >>> config.set_token('token')             # set the Eobot.com user API token

These four values also have getters and havers available:

    >>> user_id = config.get_user_id() if config.has_user_id() else 0

The ``EobotConfig`` object also offers a shorthand method to set all these values in one call:

    >>> config.configure(12345, 'user@example.com', 'password', 'token')

All arguments are optional and can also be used as named arguments. It's possible to pass ``None`` to any value to
unset it.

Note that the token usage is optional. This was added to provide distinction between the user password and the API
token, but due to the way the Eobot.com API handles token-based authentication, there is in fact no real difference
between the two. If a user has API authentication set via a token, it is also possible to just set that token as the
user's password instead. Keep in mind that if *both* are set, all API methods will always use the token.

Using ``get_config()`` without any parameters will return the global configuration that is used by default for all
methods. It is also possible to retrieve a different ``EobotConfig`` object if you want to access multiple Eobot.com
accounts in a single application, without having to update the configuration every time:

    >>> named_config = eobot.get_config("named")
    >>> eobot.get_balances(config=named_config)

All methods return the ``EobotConfig`` instance so that methods can be easily chained:

    >>> eobot.get_config().configure(user_id=12345)
    >>> eobot.get_balances()
    >>> eobot.get_balances(eobot.get_config("named").configure(user_id=67890))

Anonymous methods
-----------------

For some API methods, authentication is optional. These are the methods that return generic information that is not
specific to individual Eobot.com user accounts:

    >>> eobot.get_supported_coins()                      # Will return a list of all Cryptocurrencies supported by Eobot.com
    >>> eobot.get_supported_fiat()                       # Will return a list of all fiat currencies supported by Eobot.com
    >>> eobot.get_coin_value("BTC")                      # Will return the current BTC value in US Dollars
    >>> eobot.get_exchange_rate("EUR")                   # Will return the current exchange rate from US Dollars to Euros
    >>> eobot.get_exchange_estimate("BTC", "DOGE", 1.0)  # Will estimate the amount of DOGE you'd get for 1.0 BTC

Read-only methods
-----------------

For read-only methods, only the Eobot.com user ID is needed. If you don't know the user ID for the account you want to
query, you can obtain it via the ``get_user_id()`` method, which requires that the user's email address and password
(or token) is set:

    >>> user_id = eobot.get_user_id(eobot.get_config().configure(email='user@example.com', password='password'))

The read-only methods are:

    >>> eobot.get_config().configure(user_id=12345)
    >>> eobot.get_balances()              # Will return the current balances for all supported Cryptocurrencies
    >>> eobot.get_deposit_address("BTC")  # Will return a BTC wallet address for depositing BTC to this Eobot account
    >>> eobot.get_mining_estimates()      # Will return the estimated monthly revenue for all current miners
    >>> eobot.get_mining_mode()           # Will return the Cryptocurrency that is currently being mined
    >>> eobot.get_mining_speed()          # Will return the mining speed for all current miners

Write methods
-------------

For write methods, you will always need the user's email address and password/token. Most methods also require the
user's account ID, but this is implicitly fetched via the ``get_user_id()`` method if not provided. This does mean that
an extra API call is performed, so for performance reasons it's better to provide it yourself.

The write methods are:

    >>> eobot.get_config().configure(email='user@example.com', password='password')
    >>> eobot.exchange_coins("BTC", 1.0, "DOGE")                      # Will convert 1.0 BTC to DOGE
    >>> eobot.manual_withdraw("BTC", 1.0, "<wallet address>")         # Will withdraw 1.0 BTC to the provided wallet
    >>> eobot.set_automatic_withdraw("BTC", 1.0, "<wallet address>")  # Will configure Eobot.com to automatically withdraw BTC to the provided wallet if the balance exceeds 1.0 BTC
    >>> eobot.set_mining_mode("BTC")                                  # Will set the mining mode for the account to BTC

If you find any bugs, please raise an issue on Github.

Happy coding!