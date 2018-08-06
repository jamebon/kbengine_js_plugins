kbengine_js_plugins
========================
示例（2017/7/22）
---------------------
        1、有疑问请联系我jamebon@qq.com，就不要在wiki上面留言了，好像wiki这样的用法不对，哈哈~~~
	2、这边提供了一个简单的集成demo： https://github.com/jamebon/CreatorPluginsDemo
	3、如果我的付出对你有用，欢迎点赞~~~

改动（2017/7/6）
---------------------
	由于Cocos Creator使用严格模式的js，而原本的kbengine_js_plugins是非严格模式的，因此为了兼容和方
	便Cocos Creator开发的同学，本人对此脚本做了相应的修改，并共享出来。
	1：修改了继承的实现方式（旧版本使用callee，严格模式下不能用callee）
	2：变量名的定义（严格模式下，全局变量需要显式声明，并且为了防止全局命名污染，因此在变量名前加入了var声明为局部变量）
	3：导出KBEngine对象
	4：集成GameObject在该插件中
	
在Cocos Creator使用此脚本的步骤（2017/7/6）
---------------------
	1：复制此脚本到Creator新建的工程的脚本目录下（不要使用插件模式），请参考cocos官方文档：
	http://www.cocos.com/docs/creator/scripting/plugin-scripts.html
	http://www.cocos.com/docs/creator/scripting/third-party-module.html
	
	2：然后可以定义自己的实体类文件例如，Account.js 
	var KBEngine = require("kbengine");//使用require引入KBEngine
	/*-----------------------------------------------------------------------------------------
													entity
	-----------------------------------------------------------------------------------------*/
	KBEngine.Account = KBEngine.GameObject.extend(
	{
		__init__ : function()
		{
			this._super();
			KBEngine.Event.fire("onLoginSuccessfully", KBEngine.app.entity_uuid, this.id, this);
		},
	});
	3：或者在cocos creator的组件类里面使用，例如：ClientApp.js
	var KBEngine = require("kbengine");//使用require引入KBEngine
	cc.Class({
		extends: cc.Component,

		properties: {
			ip : "127.0.0.1",
			port:"20013",
		},

		// use this for initialization
		onLoad: function () {
			var args = new KBEngine.KBEngineArgs();
			args.ip = this.ip;
			args.port = this.port;
			KBEngine.create(args);
		},
	});

Usage
---------------------

	1: Create KBEngine
		// Initialization
		var args = new KBEngine.KBEngineArgs();
		
		args.ip = "127.0.0.1";
		args.port = 20013;
		KBEngine.create(args);

	2: Implment the KBE defined entity (including the client part)
		See: kbengine\kbengine_demos_assets\scripts\entities.xml，hasClient="true" need to implment
			<Account hasClient="true"></Account>
			<Monster hasClient="true"></Monster>
			<Gate hasClient="true"></Gate>
			<Space/>

			KBEngine.Account = KBEngine.Entity.extend(
			{
				// entity initialization
				__init__ : function()
				{
					this._super();
				}
			}

		Call entity server method
			entity.baseCall("base_func", 1, "arg2", "argN")
			entity.cellCall("cell_func", 1, "arg2", "argN")

	3: Monitor KBE-plugins event
		For example:
			var StartSceneLayer = Class.extend({
			{
				installEvents : function()
				{
					KBEngine.Event.register("onConnectionState", this, "onConnectionState");
				}

				onConnectionState : function(success)
				{
					// KBE-plugins event fired
				}
			}

	4: Fire events to the KBE-plugins
		For example:
			KBEngine.Event.fire("login", this.usernamebox.getString(), this.passwordbox.getString(), "kbengine_cocos2d_js_demo");  



KBE-Plugin fire-out events(KBE => Unity):
---------------------

	Entity events:
		onEnterWorld
			Description: 
				Entity enter the client-world.

			Event-datas: 
				Enity
				

		onLeaveWorld
			Description: 
				Entity leave the client-world.

			Event-datas: 
				Enity

		onEnterSpace
			Description: 
				Player enter the new space.

			Event-datas: 
				Enity

		onLeaveSpace
			Description: 
				Player enter the space.

			Event-datas: 
				Enity

		onCreateAccountResult
			Description: 
				Create account feedback results.

			Event-datas: 
				uint16: retcode
					http://kbengine.org/docs/configuration/server_errors.html

				bytes: datas
					If you use third-party account system, the system may fill some of the third-party additional datas.

		onControlled
			Description: 
				Triggered when the entity is controlled or out of control.

			Event-datas: 
				Enity
				bool: isControlled

		onLoseControlledEntity
			Description: 
				Lose controlled entity.

			Event-datas: 
				Enity

		set_position
			Description: 
				Sets the current position of the entity.

			Event-datas: 
				Enity

		set_direction
			Description: 
				Sets the current direction of the entity.

			Event-datas: 
				Enity

		updatePosition
			Description: 
				The entity position is updated, you can smooth the moving entity to new location.

			Event-datas: 
				Enity

	Protocol events:
		onVersionNotMatch
			Description: 
				Engine version mismatch.

			Event-datas: 
				string: clientVersion
				string: serverVersion

		onScriptVersionNotMatch
			Description: 
				script version mismatch.

			Event-datas: 
				string: clientScriptVersion
				string: serverScriptVersion

		Loginapp_importClientMessages
			Description: 
				Importing the message protocol for loginapp and client.

			Event-datas: 
				No datas.

		Baseapp_importClientMessages
			Description: 
				Importing the message protocol for baseapp and client.

			Event-datas: 
				No datas.

		Baseapp_importClientEntityDef
			Description: 
				Protocol description for importing entities.

			Event-datas: 
				No datas.

	Login and Logout status:
		onLoginBaseapp
			Description: 
				Login to baseapp.

			Event-datas: 
				No datas.

		onReloginBaseapp
			Description: 
				Relogin to baseapp.

			Event-datas: 
				No datas.

		onKicked
			Description: 
				Kicked of the current server.

			Event-datas: 
				uint16: retcode
					http://kbengine.org/docs/configuration/server_errors.html

		onLoginFailed
			Description: 
				Login failed.

			Event-datas: 
				uint16: retcode
					http://kbengine.org/docs/configuration/server_errors.html

		onLoginBaseappFailed
			Description: 
				Login baseapp failed.

			Event-datas: 
				uint16: retcode
					http://kbengine.org/docs/configuration/server_errors.html

		onReloginBaseappFailed
			Description: 
				Relogin baseapp failed.

			Event-datas: 
				uint16: retcode
					http://kbengine.org/docs/configuration/server_errors.html

		onReloginBaseappSuccessfully
			Description: 
				Relogin baseapp success.

			Event-datas: 
				No datas.
	
	Space events:
		addSpaceGeometryMapping
			Description: 
				The current space is specified by the geometry mapping.
				Popular said is to load the specified Map Resources.

			Event-datas: 
				string: resPath

		onSetSpaceData
			Description: 
				Server spaceData set data.

			Event-datas: 
				int32: spaceID
				string: key
				string value

		onDelSpaceData
			Description: 
				Server spaceData delete data.

			Event-datas: 
				int32: spaceID
				string: key

	Network events:
		onConnectionState
			Description: 
				Status of connection server.

			Event-datas: 
				bool: success or fail

		onDisconnected
			Description: 
				Status of connection server.

			Event-datas: 
				No datas.



KBE-Plugin fire-in events(Unity => KBE):
---------------------

	createAccount
			Description: 
				Create new account.

			Event-datas: 
				string: accountName
				string: password
				bytes: datas
					Datas by user defined.
					Data will be recorded into the KBE account database, you can access the datas through the script layer.
					If you use third-party account system, datas will be submitted to the third-party system.
				

	login
			Description: 
				Login to server.

			Event-datas: 
				string: accountName
				string: password
				bytes: datas
					Datas by user defined.
					Data will be recorded into the KBE account database, you can access the datas through the script layer.
					If you use third-party account system, datas will be submitted to the third-party system.

	reloginBaseapp
			Description: 
				Relogin to baseapp.

			Event-datas: 
				No datas.

	resetPassword
			Description: 
				Reset password.

			Event-datas: 
				string: accountName

	newPassword
			Description: 
				Request to set up a new password for the account.
				Note: account must be online

			Event-datas: 
				string: old_password
				string: new_password

	bindAccountEmail
			Description: 
				Request server binding account Email.
				Note: account must be online

			Event-datas: 
				string: emailAddress



