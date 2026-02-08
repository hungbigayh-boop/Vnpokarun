local Players=game:GetService("Players")
local RunService=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local RS=game:GetService("ReplicatedStorage")

local player=Players.LocalPlayer
local cam=workspace.CurrentCamera

local MenuOpen=true
local AutoQuest=false
local Aim=false
local AutoZ=false

local AIM=0.08
local Z_DELAY=0.15
local Q_DELAY=1

local Remotes=RS:FindFirstChild("Remotes") or Instance.new("Folder",RS)
Remotes.Name="Remotes"
local Quest=Remotes:FindFirstChild("Quest") or Instance.new("RemoteEvent",Remotes)
Quest.Name="Quest"
local SkillZ=Remotes:FindFirstChild("SkillZ") or Instance.new("RemoteEvent",Remotes)
SkillZ.Name="SkillZ"

local gui=Instance.new("ScreenGui",player.PlayerGui)
local frame=Instance.new("Frame",gui)
frame.Size=UDim2.fromOffset(260,220)
frame.Position=UDim2.fromScale(0.5,0.5)-UDim2.fromOffset(130,110)
frame.BackgroundColor3=Color3.fromRGB(25,25,25)
frame.Visible=true

local function btn(y,t)
	local b=Instance.new("TextButton",frame)
	b.Size=UDim2.fromOffset(240,36)
	b.Position=UDim2.fromOffset(10,y)
	b.Text=t
	return b
end

local b1=btn(20,"Auto Quest: OFF")
local b2=btn(66,"Aim Assist: OFF")
local b3=btn(112,"Auto Z: OFF")
local b4=btn(158,"Menu")

b1.MouseButton1Click:Connect(function()
	AutoQuest=not AutoQuest
	b1.Text="Auto Quest: "..(AutoQuest and "ON" or "OFF")
end)

b2.MouseButton1Click:Connect(function()
	Aim=not Aim
	b2.Text="Aim Assist: "..(Aim and "ON" or "OFF")
end)

b3.MouseButton1Click:Connect(function()
	AutoZ=not AutoZ
	b3.Text="Auto Z: "..(AutoZ and "ON" or "OFF")
end)

b4.MouseButton1Click:Connect(function()
	MenuOpen=not MenuOpen
	frame.Visible=MenuOpen
end)

UIS.InputBegan:Connect(function(i,g)
	if g then return end
	if i.KeyCode==Enum.KeyCode.RightShift then
		MenuOpen=not MenuOpen
		frame.Visible=MenuOpen
	end
end)

local function boss()
	for _,m in ipairs(workspace:GetChildren()) do
		if m.Name=="OkakaBoss" and m:FindFirstChild("HumanoidRootPart") then
			return m
		end
	end
end

RunService.RenderStepped:Connect(function()
	if not Aim then return end
	local b=boss()
	if not b then return end
	local cf=CFrame.new(cam.CFrame.Position,b.HumanoidRootPart.Position)
	cam.CFrame=cam.CFrame:Lerp(cf,AIM)
end)

task.spawn(function()
	while true do
		if AutoQuest then
			Quest:FireServer("Okaka")
		end
		task.wait(Q_DELAY)
	end
end)

task.spawn(function()
	while true do
		if AutoZ then
			SkillZ:FireServer()
		end
		task.wait(Z_DELAY)
	end
end)
