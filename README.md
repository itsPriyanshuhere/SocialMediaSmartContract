# SocialMediaSmartContract


pragma solidity ^0.8.0;

contract TwitterLikeDApp {
    struct User {
        string username;
        string profileData;
        address userAddress;
    }

    event NewLike(address indexed from, address indexed to, uint256 tweetId);
    event NewRetweet(address indexed from, address indexed to, uint256 tweetId);
    event Mention(address indexed from, address indexed to, uint256 tweetId);
    event NewTweet(address indexed author, uint256 indexed tweetId, string content);
    event Followed(address indexed follower, address indexed followedUser);
    event Unfollowed(address indexed follower, address indexed unfollowedUser);


    mapping(address => User) public users;

    function registerUser(string memory _username, string memory _profileData) public {
        require(users[msg.sender].userAddress == address(0), "User already registered");
        users[msg.sender] = User(_username, _profileData, msg.sender);
    }

        struct Tweet {
        uint256 tweetId;
        string content;
        address author;
        uint256 likes;
        uint256 retweets;
    }

    mapping(address => Tweet[]) public userTweets;

    function postTweet(string memory _content) public {
        require(bytes(_content).length > 0, "Tweet content cannot be empty");
        Tweet memory newTweet = Tweet(userTweets[msg.sender].length, _content, msg.sender, 0, 0);
        userTweets[msg.sender].push(newTweet);
    }

        mapping(address => address[]) public userFollowers;
    mapping(address => address[]) public userFollowing;

    function followUser(address _followedUser) public {
        userFollowing[msg.sender].push(_followedUser);
        userFollowers[_followedUser].push(msg.sender);
    }

    function unfollowUser(address _unfollowedUser) public {
        // Implement logic to remove users from each other's following/followers lists.
    }

        mapping(address => mapping(uint256 => bool)) public userLikes;
    mapping(address => mapping(uint256 => bool)) public userRetweets;

    function likeTweet(address _tweetAuthor, uint256 _tweetId) public {
        // Implement logic to record likes.
    }

    function retweet(address _tweetAuthor, uint256 _tweetId) public {
        // Implement logic to record retweets.
    }

    function disLikeTweet(address _tweetAuthor, uint256 _tweetId) public {
        // Implement logic to record likes and emit NewLike event.
        emit NewLike(msg.sender, _tweetAuthor, _tweetId);
    }

    // Implement similar functions for retweets and mentions.

    //Premium content transactions

        mapping(address => uint256) public userBalance;

    function tipUser(address _recipient, uint256 _amount) public {
        require(_amount > 0, "Amount must be greater than zero");
        require(userBalance[msg.sender] >= _amount, "Insufficient balance");
        
        userBalance[msg.sender] -= _amount;
        userBalance[_recipient] += _amount;
    }

    // Implement a function to withdraw funds from the contract to the user's wallet.
    function withdrawFunds(uint256 _amount) public {
        require(userBalance[msg.sender] >= _amount, "Insufficient balance");
        
        userBalance[msg.sender] -= _amount;
        payable(msg.sender).transfer(_amount);
    }

        mapping(uint256 => bool) public flaggedTweets;

    function flagTweet(uint256 _tweetId) public {
        flaggedTweets[_tweetId] = true;
    }

    // Implement a function to review flagged tweets and take appropriate actions.

        mapping(address => mapping(address => bool)) public delegatedPermissions;

    modifier hasPermission(address _delegate, string memory _permission) {
        require(delegatedPermissions[msg.sender][_delegate], "Permission denied");
        _;
    }

    function grantPermission(address _delegate, string memory _permission) public {
        delegatedPermissions[msg.sender][_delegate] = true;
    }

    // Implement functions for revoking permissions and checking permissions before actions.

        function getTweetAnalytics(address _user) public view returns (uint256 totalTweets, uint256 totalLikes, uint256 totalRetweets) {
        Tweet[] storage tweets = userTweets[_user];
        for (uint256 i = 0; i < tweets.length; i++) {
            totalTweets++;
            totalLikes += tweets[i].likes;
            totalRetweets += tweets[i].retweets;
        }
    }

        struct Content {
        string contentHash; // IPFS or other content address
        uint256 timestamp;
        address owner;
    }

    mapping(uint256 => Content) public contentMap;
    uint256 public contentCounter = 0;

    function storeContent(string memory _contentHash) public {
        contentMap[contentCounter] = Content(_contentHash, block.timestamp, msg.sender);
        contentCounter++;
    }

   // block or unblock user
   // nft profile picture
   // koi bhi buy kar sakta uska certain fraction mil jayega uska

}
