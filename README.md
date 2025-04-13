# ra
..
// ✅ app/page.tsx
"use client";

import React, { useState, useEffect } from "react";
import { useSession, signOut } from "next-auth/react";
import { redirect } from "next/navigation";
import { Button } from "@/components/ui/button";

const ProfilePage = () => {
  const { data: session, status } = useSession();
  const [username, setUsername] = useState("Your Name");
  const [profileImage, setProfileImage] = useState<string | null>(null);
  const [storyImage, setStoryImage] = useState<string | null>(null);
  const [storyText, setStoryText] = useState("");
  const [stories, setStories] = useState<{ text: string; image: string | null }[]>([]);
  const [chatName, setChatName] = useState("");
  const [bio, setBio] = useState("");

  useEffect(() => {
    if (session?.user?.email) {
      setUsername(session.user.email.split("@")[0]);
    }
  }, [session]);

  if (status === "loading") return <p className="text-center mt-10 text-white">Loading...</p>;
  if (!session) {
    redirect("/signin");
  }

  const handleProfileImageChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    if (event.target.files && event.target.files[0]) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.onloadend = () => setProfileImage(reader.result as string);
      reader.readAsDataURL(file);
    }
  };

  const handleStoryImageChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    if (event.target.files && event.target.files[0]) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.onloadend = () => setStoryImage(reader.result as string);
      reader.readAsDataURL(file);
    }
  };

  const handleAddStory = () => {
    if (storyText || storyImage) {
      setStories([{ text: storyText, image: storyImage }, ...stories]);
      setStoryText("");
      setStoryImage(null);
    }
  };

  return (
    <div className="flex flex-col items-center p-6 space-y-6 bg-black text-white min-h-screen">
      <h2 className="text-3xl font-extrabold text-pink-300">Welcome, {username}</h2>

      <Button onClick={() => signOut()} className="bg-pink-600 hover:bg-pink-500 text-white">Sign Out</Button>

      <div className="flex flex-col items-center">
        <label className="text-lg font-medium">Display Name</label>
        <input
          type="text"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
          className="p-2 mt-2 border rounded-md w-64 text-black focus:outline-none"
        />
      </div>

      <div className="flex flex-col items-center">
        <label className="text-lg font-medium">Bio</label>
        <textarea
          value={bio}
          onChange={(e) => setBio(e.target.value)}
          placeholder="Tell us something about you..."
          className="p-2 mt-2 border rounded-md w-64 text-black focus:outline-none"
        />
      </div>

      <div className="flex flex-col items-center">
        <label className="text-lg font-medium">Profile Picture</label>
        <label htmlFor="profile-image-upload" className="cursor-pointer mt-2">
          <img
            src={profileImage || "https://via.placeholder.com/100"}
            alt="Profile"
            className="w-24 h-24 rounded-full border border-pink-500"
          />
        </label>
        <input
          id="profile-image-upload"
          type="file"
          accept="image/*"
          className="hidden"
          onChange={handleProfileImageChange}
        />
      </div>

      <div className="flex flex-col items-center mt-6">
        <label className="text-lg font-medium">Create a Story</label>
        <textarea
          placeholder="Write your story..."
          value={storyText}
          onChange={(e) => setStoryText(e.target.value)}
          className="mt-2 p-2 border rounded-md w-64 text-black focus:outline-none"
        />

        <label htmlFor="story-image-upload" className="cursor-pointer mt-2">
          {storyImage ? (
            <img src={storyImage} alt="Story" className="w-40 h-40 rounded-lg mt-2" />
          ) : (
            <div className="w-40 h-40 bg-gray-800 flex items-center justify-center rounded-lg text-pink-200">
              <span>Upload Story Image</span>
            </div>
          )}
        </label>
        <input
          id="story-image-upload"
          type="file"
          accept="image/*"
          className="hidden"
          onChange={handleStoryImageChange}
        />

        <Button onClick={handleAddStory} className="mt-4 bg-pink-500 hover:bg-pink-400 text-white">
          Share Story
        </Button>
      </div>

      <div className="mt-8 w-full max-w-md">
        <h3 className="text-lg font-semibold text-pink-300 mb-2">My Stories</h3>
        <div className="flex space-x-4 overflow-x-auto">
          {stories.length === 0 && <p className="text-sm text-gray-400">No stories yet.</p>}
          {stories.map((story, index) => (
            <div key={index} className="flex flex-col items-center space-y-2">
              {story.image ? (
                <img src={story.image} alt={`Story ${index}`} className="w-32 h-32 rounded-lg" />
              ) : (
                <div className="w-32 h-32 bg-gray-800 flex items-center justify-center rounded-lg text-pink-200">
                  <span>No Image</span>
                </div>
              )}
              <p className="text-sm text-pink-100 text-center">{story.text}</p>
            </div>
          ))}
        </div>
      </div>

      <div className="mt-6 w-full max-w-md">
        <h3 className="text-lg font-semibold text-pink-300">Customize Chat</h3>
        <input
          type="text"
          placeholder="Set a name for your chat"
          value={chatName}
          onChange={(e) => setChatName(e.target.value)}
          className="p-2 mt-2 border rounded-md w-full text-black focus:outline-none"
        />
      </div>
    </div>
  );
};

export default ProfilePage;
