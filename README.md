//SPDX-License-Identifier:MIT
pragma solidity ^0.8.0;

contract TwitterLikeDApp {
    struct User {
        string username;
        string profileData;
        address userAddress;
    }

    // uint constant = 3;

    //eventList

    event NewLike(address indexed from, address indexed to, uint256 tweetId);
    event NewRetweet(address indexed from, address indexed to, uint256 tweetId);
    event Mention(address indexed from, address indexed to, uint256 tweetId);
    event NewTweet(address indexed author, uint256 indexed tweetId, string content);
    event Followed(address indexed follower, address indexed followedUser);
    event Unfollowed(address indexed follower, address indexed unfollowedUser);
    event TipSent(address indexed sender, address indexed recipient, uint256 amount);


    //mapping for users
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
        string uri;// addition
    }

    function updateUser(string memory newName,string memory profileData) public{
        User storage userData = users[msg.sender];
        userData.username = newName;
        userData.profileData = profileData;
    }

    //tweetId: would be the tweetLength+3;

    mapping(address => Tweet[]) public userTweets;

    function postTweet(string memory _content, string memory _uri) public {
        require(bytes(_content).length > 0, "Tweet content cannot be empty");
        Tweet memory newTweet = Tweet(userTweets[msg.sender].length+3, _content, msg.sender, 0, 0,_uri);
        userTweets[msg.sender].push(newTweet);
    }

    mapping(address => address[]) public userFollowers;
    mapping(address => address[]) public userFollowing;

    function followUser(address _followedUser) public {
        userFollowing[msg.sender].push(_followedUser);
        userFollowers[_followedUser].push(msg.sender);
    }

    function unfollowUser(address _unfollowedUser) public {

    require(userFollowing[msg.sender].length > 0, "You are not following anyone");
    require(users[_unfollowedUser].userAddress != address(0), "User not found");

    // Remove _unfollowedUser from the list of users followed by msg.sender

    uint i = 0;
    while (i < userFollowing[msg.sender].length) {
        if (userFollowing[msg.sender][i] == _unfollowedUser) {
            userFollowing[msg.sender][i] = userFollowing[msg.sender][userFollowing[msg.sender].length - 1];
            userFollowing[msg.sender].pop();
            break;
        }
        i++;
    }

    // Remove msg.sender from the list of followers of _unfollowedUser

    i = 0;
    while (i < userFollowers[_unfollowedUser].length) {
        if (userFollowers[_unfollowedUser][i] == msg.sender) {
            userFollowers[_unfollowedUser][i] = userFollowers[_unfollowedUser][userFollowers[_unfollowedUser].length - 1];
            userFollowers[_unfollowedUser].pop();
            break;
        }
        i++;
    }

    emit Unfollowed(msg.sender, _unfollowedUser);

}

    mapping(address => uint256) public creatorBalance;

    function tipUser(address _recipient, uint256 _amount) public {
    require(_amount > 0, "Amount must be greater than zero");
    require(creatorBalance[msg.sender] >= _amount, "Insufficient balance");
    require(users[_recipient].userAddress != address(0), "Recipient not found");
    
    require(msg.sender != _recipient, "You cannot tip yourself");

    creatorBalance[msg.sender] -= _amount;
    creatorBalance[_recipient] += _amount;

    emit TipSent(msg.sender, _recipient, _amount);
    }



}
