eflowdock
============

eflowdock - is erlang library, which provides flowdock chat streaming api. I made it for [Ybot](https://github.com/0xAX/Ybot) chat robot, i decided to move it in separate library as [ecampfire](https://github.com/0xAX/ecampfire), hope that it can be useful for somebody.

Installing
=============

First of all you must get source code:

```
git clone https://github.com/0xAX/eflowdock.git
```

After that you must get eflowdock's dependencies and build it:

```
./rebar get-deps && ./rebar compile
```

Usage
===========

For eflowdock usage you must wrote simple gen_server handler like this:

```erlang
-module(flowdock_test_handler).

-behaviour(gen_server).
 
-export([start_link/0]).
 
%% gen_server callbacks
-export([init/1,
         handle_call/3,
         handle_cast/2,
         handle_info/2,
         terminate/2,
         code_change/3]).
 
-record(state, {
	% flowdock client process pid
	flowdock_client_pid :: pid()
    }).
 
start_link() ->
    gen_server:start_link({local, ?MODULE}, ?MODULE, [], []).

%% @doc send message to flowdock
send_message(Pid, From, Message) ->
	gen_server:cast(Pid, {send_message, From, Message}).
 
init([]) ->
	% Start campfire client
	{ok, Pid} = eflowdock_sup:start_flowdock_client(self(), <<"FlowOrg">>, <<"Flow">>, <<"FlowdockLogin">>, <<"FlowdockPassword">>),
	% init
    {ok, #state{flowdock_client_pid = Pid}}.
 
handle_call(_Request, _From, State) ->
    {reply, ignored, State}.

handle_cast(_Msg, State) ->
    {noreply, State}.

%% @doc Receiving incoming message
handle_info({incoming_message, IncomingMessage}, State) ->
	% Do something with incoming message here
    {noreply, State};

handle_info(_Info, State) ->
    {noreply, State}.
 
terminate(_Reason, _State) ->
    ok.
 
code_change(_OldVsn, State, _Extra) ->
    {ok, State}.
```

Contribute
============

  * Fork main ybot repository (https://github.com/0xAX/eflowdock).
  * Make your changes in your clone of eflowdock.
  * Test it.
  * Send pull request.

Author
========

[@0xAX](https://twitter.com/0xAX)
