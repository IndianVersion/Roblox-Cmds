#Shared-repo

**NON-COMMERCIAL USE-ONLY**


#This is special (held-for-review only)

```LUA
--// @Author: Sqowly
--// began writing: 12/28/2020 <-- bad year btw

--// HAPPY NEW YEAR its currently 12:55 AM 1/1/2021 and i havent slept yet niceeee.

wait(2.1)
print("[LOADED JENGANBACKBONE]")
wait(.2)

local TweenService = game:GetService("TweenService")
local AgePlayers = game.ReplicatedStorage.AgePlayers
local FMCServer = game.ReplicatedStorage.FMCServer
local CheckJoined = game.ReplicatedStorage.CheckJoined
local node = game.ReplicatedStorage.node
local mergenodes = game.ReplicatedStorage.mergenodes
local kick = game.ReplicatedStorage.Kick
local UIS = game:GetService("UserInputService")

local tweens = TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.In, 0, false, 0.6)
local properties = {BackgroundTransparency = 0}

local properties_1 = {BackgroundTransparency = 1}

local tweensforcmds = TweenInfo.new(0.4, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out, 0, false, 0.7)

local SpliceCommands = script.Parent.Parent.SpliceCommands

local SpliceCommandLine = script.Parent.Parent.SpliceCommands.SpliceCommandLine
local Execute = script.Parent.Parent.SpliceCommands.Execute

Execute.TextColor3 = Color3.fromRGB(35, 204, 255)

local Err = script.Parent.Err
local ErrLabel = Err.ErrLabel

local GatherAge = script.Parent.GatherAge
local plrAge = GatherAge.plrAge

local Cmds = script.Parent.Cmds
local CmdText = Cmds.CmdText
local Handle = CmdText.Handle

local MergenodesUI = script.Parent.MergenodesUI
local merge = MergenodesUI.merge

local ShowCommands = script.Parent.ShowCommands
local ShowCommandsButton = ShowCommands.ShowCommandsButton

local ForMultipleClients = script.Parent.ForMutlipleClients
local SignalClient = ForMultipleClients.SignalClient

local create_add = TweenService:Create(Execute, tweens, properties)
local create_remove = TweenService:Create(Execute, tweens, properties_1)

local Spliced = {}

local PossibleCommands={
	[1]="%log(",
	[2]="%kick(",
	[3]="%relog", --// kills program and reloads
	[4]="%age(",
	[5]="%buildnode" --// builds a node
}

local Owner_Users={
	[1]="Sqowly", --// writer (DO NOT remove!!!111!1)
	[2]="OrcaKillerz", 
	[3]="KillerStormz",
	[4]="Disgusting1337"
}

local function reload()
	script.Parent.Parent.SpliceCommands.Enabled = false
	wait(1.5)
	script.Parent.Parent.SpliceCommands.Enabled = true
end

--// checking for who own the software is always a long task but it gets done :D
CheckJoined.OnClientEvent:Connect(function()
	for _, owners in pairs (Owner_Users) do
		if owners then
			for i, player in pairs (game.Players:GetPlayers()) do
				if player.Name == unpack(Owner_Users) then
					SpliceCommandLine.Visible = true
					ShowCommands.Enabled = true
					GatherAge.Enabled = true
					ForMultipleClients.Enabled = true
					ShowCommandsButton.MouseButton1Click:Connect(function()
						if Cmds.Enabled == false then
							Cmds.Enabled = true
							local close = TweenService:Create(CmdText, tweensforcmds, properties):Play()
							local close_2 = TweenService:Create(Handle, tweensforcmds, properties_1):Play()
							local close_3 = TweenService:Create(CmdText.CmdLabel, tweensforcmds, properties):Play()
							CmdText.CmdLabel.Visible = true
							Handle.Visible = true
						elseif Cmds.Enabled == true then
							Cmds.Enabled = false
						end
					end)
				elseif player.Name ~= unpack(Owner_Users) then
					ShowCommands.Enabled = false
					SpliceCommandLine.Visible = false
					GatherAge.Enabled = false
					ForMultipleClients.Enabled = false
					return;
				end
			end
		end
	end
end)

local cd = true

UIS.InputBegan:Connect(function(input, isTyping)
	if isTyping then return end
	if input.KeyCode == Enum.KeyCode.KeypadPeriod and cd then --// when q is pressed down it will focus onto textbox
		cd = false
		SpliceCommandLine:CaptureFocus()
		Execute.Visible = true
		create_add:Play()
		wait(.1)
		Execute.Text = "Execute"
		SpliceCommandLine.Text = ""
		wait(.71)
		cd = true
	end
end)

SpliceCommandLine.Focused:Connect(function()
	Execute.Visible = true
	create_add:Play()
	wait(.2)
	Execute.Text = "Execute"
end)

SpliceCommandLine.FocusLost:Connect(function()
	create_remove:Play()
	wait(.2)
	Execute.Text = ""
	wait(2.5)
	Execute.Visible = false
	Execute.MouseButton1Click:Connect(function()
		wait(.2)
		if SpliceCommandLine.Text == "" then
			print("Can't function with: "..(SpliceCommandLine.Text)..(" a/o blank")..(" or ")..(tostring(nil)))
			ErrLabel.Visible = true
			wait(2.3)
			ErrLabel.Visible = false
			return; --// stop
		else
			table.insert(Spliced, string.split(SpliceCommandLine.Text, "'"))
			for _, SplicedText in pairs (Spliced) do
				if SplicedText[1] == PossibleCommands[1] then
					--// add interface that fires to all clients
					FMCServer:FireServer(SplicedText[2], SignalClient)
				elseif SpliceCommandLine.Text == PossibleCommands[3] then
					reload()
				elseif SplicedText[1] == PossibleCommands[2] then
					local User = Instance.new("StringValue", game.ReplicatedStorage.Users)
					for index = 1, #game.ReplicatedStorage.Users:GetChildren(), 1 do
						User.Name = "User-"..(index)
						User.Value = SplicedText[2]
						if User.Value ~= "" or " " then
							kick:FireServer(SplicedText[2])
						else
							return;
						end
					end
				elseif SplicedText[1] == PossibleCommands[4] then
					--// age player of certain name here
					GatherAge.Enabled = false
					AgePlayers:FireServer(SplicedText[2])
				elseif SpliceCommandLine.Text == PossibleCommands[5] then
					node:FireServer()
				end
			end
		end
	end)
	return;
end)

mergenodes.OnClientEvent:Connect(function()
	MergenodesUI.Enabled = true
	merge.MouseButton1Click:Connect(function()
		local form = game.ReplicatedStorage.form
		form:FireServer()
		return;
	end)
end)
```
