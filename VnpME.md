local RunService=game:GetService("RunService")
local Players=game:GetService("Players")
local RS=game:GetService("ReplicatedStorage")

if RunService:IsServer() then
	local Remotes=RS:FindFirstChild("Remotes") or Instance.new("Folder",RS)
	Remotes.Name="Remotes"

	local Quest=Remotes:FindFirstChild("Quest") or Instance.new("RemoteEvent",Remotes)
	Quest.Name="Quest"

	local SkillZ=Remotes:FindFirstChild("SkillZ") or Instance.new("RemoteEvent",Remotes)
	SkillZ.Name="SkillZ"

	Quest.OnServerEvent:Connect(function(player,name)
		if name=="Okaka" then
			player:SetAttribute("QuestOkaka",true)
		end
	end)

	SkillZ.OnServerEvent:Connect(function(player)
		if not player:GetAttribute("QuestOkaka") then return end
		local boss=workspace:FindFirstChild("OkakaBoss")
		if boss and boss:FindFirstChild("Humanoid") then
			boss.Humanoid:TakeDamage(50)
		end
	end)

	Players.PlayerAdded:Connect(function(plr)
		plr:SetAttribute("QuestOkaka",false)
		local ls=Instance.new("LocalScript")
		ls.Source=[[
local RS=game:GetService("ReplicatedStorage")
local RunService=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local player=game.Players.LocalPlayer
local cam=workspace.CurrentCamera

local Quest=RS.Remotes.Quest
local SkillZ=RS.Remotes.SkillZ

local autoQuest=false
local aim=false
local autoZ=false
local menu=true

local gui=Instance.new("ScreenGui",player.PlayerGui)
local f=Instance.new("Frame",gui)
f.Size=UDim2.fromOffset(240,180)
f.Position=UDim2.fromScale(.5,.5)-UDim2.fromOffset(120,90)
f.BackgroundColor3=Color3.fromRGB(25,25,25)

local function btn(y,t)
	local b=Instance.new("TextButton",f)
	b.Size=UDim2.fromOffset(220,40)
	b.Position=UDim2.fromOffset(10,y)
	b.Text=t
	return b
end

local b1=btn(10,"Auto Quest OFF")
local b2=btn(55,"Aim OFF")
local b3=btn(100,"Auto Z OFF")

b1.MouseButton1Click:Connect(function()
	autoQuest=not autoQuest
	b1.Text=autoQuest and "Auto Quest ON" or "Auto Quest OFF"
end)

b2.MouseButton1Click:Connect(function()
	aim=not aim
	b2.Text=aim and "Aim ON" or "Aim OFF"
end)

b3.MouseButton1Click:Connect(function()
	autoZ=not autoZ
	b3.Text=autoZ and "Auto Z ON" or "Auto Z OFF"
end)

UIS.InputBegan:Connect(function(i,g)
	if g then return end
	if i.KeyCode==Enum.KeyCode.RightShift then
		menu=not menu
		f.Visible=menu
	end
end)

local function boss()
	return workspace:FindFirstChild("OkakaBoss")
end

RunService.RenderStepped:Connect(function()
	if aim and boss() then
		cam.CFrame=cam.CFrame:Lerp(
			CFrame.new(cam.CFrame.Position,boss().HumanoidRootPart.Position),
			0.1
		)
	end
end)

task.spawn(function()
	while true do
		if autoQuest then Quest:FireServer("Okaka") end
		task.wait(1)
	end
end)

task.spawn(function()
	while true do
		if autoZ then SkillZ:FireServer() end
		task.wait(0.15)
	end
end)
]]
		ls.Parent=plr:WaitForChild("PlayerGui")
	end)
end
